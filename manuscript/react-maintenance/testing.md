## 从单元测试到集成测试

测试代码对于编程来说是至关重要的，应该被视为认真的开发人员的一项必修课。在产品环境使用前，我们要先验证我们的源代码的质量和功能。[测试金字塔](https://martinfowler.com/articles/practical-test-pyramid.html)将作为我们的指南。

测试金字塔包含了端到端测试，基础测试和单元测试。单元测试用于小而独立的代码块，比如一个函数或组件。集成测试帮助我们弄清楚这些单元是否能够很好地协作。端到端测试模拟了一个真实的情景，比如一个网页应用的登录流程。单元测试的编写很快，维护也很简单；端到端测试则相反。

我们希望有许多单元测试来覆盖函数和组件。之后，我们可以使用一部分集成测试来确保最重要的函数和组件按照我们预期的方式协作。最后，我们或许需要一些端到端测试来模拟关键情景。在这次学习中，我们将覆盖**单元测试和集成测试**，以及一种被称为**快照测试**的特殊的组件测试技术。**端到端测试**将是本次练习的一部分。

![](images/testing-pyramid.png)

由于有[许多测试库](https://www.robinwieruch.de/react-testing-tutorial)，对于初学者来说从中选择一个可能是一个挑战。我们将使用 FaceBook 的 [Jest](https://jestjs.io/) 作为测试框架，以避免让本教程过于主观。其他大部分 React 的测试库以 Jest 作为基础，所以这也是其足够好的一个证明。

### 从单元测试到集成测试

通常来说单元测试和集成测试的边界是模糊的。测试包含 Item 组件的 List 组件可以被看做是一个集成测试，但是它也可以被看做是对于两个高耦合组件的单元测试。在这一小节中，我们从单元测试开始，逐渐向集成测试过渡。这两者之间的过渡如光谱一样模糊没有边界。

让我们在 *src/App.test.js* 目录下以一个伪测试作为开始吧：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('something truthy', () => {
  it('true to be true', () => {
    expect(true).toBe(true);
  });
});
~~~~~~~

幸运的是，create-react-app 自带 Jest。你可以在命令行中使用交互式 create-react-app 测试脚本来运行测试。所有测试用例的输出都将显示在你的命令行界面中。

{title="Command Line",lang="text"}

~~~~~~~
npm test
~~~~~~~

Jest 在命令运行的时候会匹配所有文件名后缀为 *test.js* 的文件。运行成功的测试将显示为绿色；失败的则显示为红色：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('something truthy', () => {
  it('true to be true', () => {
    expect(true).toBe(false);
  });
});
~~~~~~~

Jest 里的测试由**测试套件**(`describe`)组成，而测试套件又由**测试用例**(`it`)组成，测试用例中的**断言**(`expect`)来决定测试是否通过：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
// test suite
describe('truthy and falsy', () => {
  // test case
  it('true to be true', () => {
    // test assertion
    expect(true).toBe(true);
  });

  // test case
  it('false to be false', () => {
    // test assertion
    expect(false).toBe(false);
  });
});
~~~~~~~

`it` 块描述了一个测试用例。它附带了一个在测试成功或失败时返回的测试描述。我们也可以将这个块写入 `describe` 块，该块用许多个 `it` 块定义了一个针对特殊组件的测试套件。这两个块都用来组织你的测试用例。需要注意的是 `it` 函数在 JavaScript 社区中被称为单测试用例函数；但是在 Jest 中，`it` 通常被用做 `test` 函数的别名。

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('something truthy', () => {
# leanpub-start-insert
  test('true to be true', () => {
# leanpub-end-insert
    expect(true).toBe(false);
  });
});
~~~~~~~

为了在 Jest 中使用 React 组件，我们引入了一个在测试环境渲染组件的实用的库：

{title="Command Line",lang="text"}

~~~~~~~
npm install react-test-renderer --save-dev
~~~~~~~

当然了，在你测试你的组件前，你必须从你的 *src/App.js* 文件中将其导出：

{title="src/App.js",lang="javascript"}

~~~~~~~
...

export default App;

# leanpub-start-insert
export { SearchForm, InputWithLabel, List, Item };
# leanpub-end-insert
~~~~~~~

在 *src/App.test.js* 文件中将组件连同之前安装好的工具库一起引入：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
# leanpub-start-insert
import React from 'react';
import renderer from 'react-test-renderer';

import App, { Item, List, SearchForm, InputWithLabel } from './App';
# leanpub-end-insert
~~~~~~~

为 Item 组件编写第一个测试。该测试用例用工具库渲染出给定的 `item` 组件：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
import React from 'react';
import renderer from 'react-test-renderer';

import App, { Item, List, SearchForm, InputWithLabel } from './App';

# leanpub-start-insert
describe('Item', () => {
  const item = {
    title: 'React',
    url: 'https://reactjs.org/',
    author: 'Jordan Walke',
    num_comments: 3,
    points: 4,
    objectID: 0,
  };

  it('renders all properties', () => {
    const component = renderer.create(<Item item={item} />);

    expect(component.root.findByType('a').props.href).toEqual(
      'https://reactjs.org/'
    );
  });
});
# leanpub-end-insert
~~~~~~~

组件或元素的属性信息可以通过 `porps` 属性获取到。在测试的断言中，我们获取到锚点标签(`a`)和它的 `href` 属性，同时执行了一个可用性检查。如果测试变绿，我们就可以确定 Item 组件锚点标签的 `href` 属性被设置了正确的 `url` 属性。在同一个测试用例中，我们可以为其他 Item 组件的属性增加更多的测试断言：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('Item', () => {
  const item = {
    title: 'React',
    url: 'https://reactjs.org/',
    author: 'Jordan Walke',
    num_comments: 3,
    points: 4,
    objectID: 0,
  };

  it('renders all properties', () => {
    const component = renderer.create(<Item item={item} />);

    expect(component.root.findByType('a').props.href).toEqual(
      'https://reactjs.org/'
    );

# leanpub-start-insert
    expect(
      component.root.findAllByType('span')[1].props.children
    ).toEqual('Jordan Walke');
# leanpub-end-insert
  });
});
~~~~~~~

因为有多个 `span` 元素，我们先获取到所有的 `span` 元素然后选择第二个（索引为1，因为从0开始计数）再将其 React 的 `children` 属性与 Item 组件的 `author` 作对比。不过，这个测试还不够彻底。一旦 Item 组件中的 span 元素的顺序发生变化，测试就会失败。为了避免这样的情况，可以将断言改成：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('Item', () => {
  const item = { ... };

  it('renders all properties', () => {
    ...

# leanpub-start-insert
    expect(
      component.root.findAllByProps({ children: 'Jordan Walke' })
        .length
    ).toEqual(1);
# leanpub-end-insert
  });
});
~~~~~~~

这下测试断言不再那么具体了。它只是测试了是否有一个元素具有 Item 的 `author` 属性。你可以用这种方式去测试 Item 其他的属性。或者你可以把它们留作后面的练习。

我们已经测试了 Item 组件是否如期渲染了字符或者 HTML 属性(`href`)，但是我们还没有测试回调处理函数。下面的测试用例通过 `button` 元素的 `onClick` 属性模拟了一个点击事件，从而实现了这个断言。

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('Item', () => {
  const item = { ... };

  it('renders all properties', () => {
    ...
  });

# leanpub-start-insert
  it('calls onRemoveItem on button click', () => {
    const handleRemoveItem = jest.fn();

    const component = renderer.create(
      <Item item={item} onRemoveItem={handleRemoveItem} />
    );

    component.root.findByType('button').props.onClick();

    expect(handleRemoveItem).toHaveBeenCalledTimes(1);
    expect(handleRemoveItem).toHaveBeenCalledWith(item);

    expect(component.root.findAllByType(Item).length).toEqual(1);
  });
# leanpub-end-insert
});
~~~~~~~

Jest 允许我们将一个特殊的测试函数当做 Item 组件的 prop 传入。这些函数被称为 **spy** 、 **stub** 或 **mock**；它们被用做不同的测试场景。`jest.fn()` 将返回一个真实函数的 mock，这可以让我们捕捉到函数什么时候被调用。因此，我们可以使用 `toHaveBeenCalledTimes` 断言来断言函数被调用的次数；也可以用 `toHaveBeenCalledWith` 来验证传入函数的参数。

Item 组件的单元测试到这里就完成了，因为我们已经测试了输入(`item`)和输出(`onRemoveItem`)。不要将这两者与已经测试过的函数组件的输入（参数）和输出（JSX）混淆。最后的一个改进是增加一个公有的设置函数来让 Item 组件的测试更加简洁：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('Item', () => {
  const item = { ... };
# leanpub-start-insert
  const handleRemoveItem = jest.fn();
# leanpub-end-insert

# leanpub-start-insert
  let component;
# leanpub-end-insert

# leanpub-start-insert
  beforeEach(() => {
    component = renderer.create(
      <Item item={item} onRemoveItem={handleRemoveItem} />
    );
  });
# leanpub-end-insert

  it('renders all properties', () => {
    expect(component.root.findByType('a').props.href).toEqual(
      'https://reactjs.org/'
    );

    ...
  });

  it('calls onRemoveItem on button click', () => {
    component.root.findByType('button').props.onClick();

    ...
  });
});
~~~~~~~

在测试中，一个公有的设置（或拆卸）函数可以消除重复代码。因为组件必须在两个测试中渲染，而且两次渲染的 props 是相同的，所以我们可以将这部分代码放到一个公有的设置函数中。从这里开始，我们将进入 List 组件的测试：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
...

describe('Item', () => {
  ...
});

# leanpub-start-insert
describe('List', () => {
  const list = [
    {
      title: 'React',
      url: 'https://reactjs.org/',
      author: 'Jordan Walke',
      num_comments: 3,
      points: 4,
      objectID: 0,
    },
    {
      title: 'Redux',
      url: 'https://redux.js.org/',
      author: 'Dan Abramov, Andrew Clark',
      num_comments: 2,
      points: 5,
      objectID: 1,
    },
  ];

  it('renders two items', () => {
    const component = renderer.create(<List list={list} />);

    expect(component.root.findAllByType(Item).length).toEqual(2);
  });
});
# leanpub-end-insert
~~~~~~~

该测试直接检查了在 List 组件中是否以两个 item 属性渲染了两个 Item 组件。你可以使用上一个 Item 组件测试中类似的方式，接着测试 List 组件的每一个回调处理函数(`onRemoveItem`)是否在每一个 Item 组件中被调用。那么这个这样的测试还是一个单元测试吗？或者说这已经属于集成测试了呢？

先把这个问题放一放，我们将继续测试带有 InputWithLabel 组件的 SearchForm 组件：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
# leanpub-start-insert
describe('SearchForm', () => {
  const searchFormProps = {
    searchTerm: 'React',
    onSearchInput: jest.fn(),
    onSearchSubmit: jest.fn(),
  };

  let component;

  beforeEach(() => {
    component = renderer.create(<SearchForm {...searchFormProps} />);
  });

  it('renders the input field with its value', () => {
    const value = component.root.findByType(InputWithLabel).props
      .value;

    expect(value).toEqual('React');
  });
});
# leanpub-end-insert
~~~~~~~

在这个测试中，我们断言了 InputWithLabel 组件是否从 SearchForm 组件中接收到了正确的 prop。本质上来说这个测试在 InputWithLabel 组件之前就结束了，因为它只测试了组件的接口(props)。可以说它仍然是一个单元测试，因为 InputWithLabel 组件的内部实现细节可以在不改变接口的情况下改变。你可以通过改变测试来获取到 InputWithLabel 组件内 input 组件的字段，因为所有的子组件和元素也会被渲染。

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('SearchForm', () => {
  ...

  it('renders the input field with its value', () => {
# leanpub-start-insert
    const value = component.root.findByType('input').props.value;
# leanpub-end-insert

    expect(value).toEqual('React');
  });
});
~~~~~~~

这是我们第一次在 SearchForm 组件和 InputWithLabel 组件之间进行集成测试，这两个组件并不像 List 组件和 Item 组件那样高耦合。InputWithLabel 组件可以在其他组件中使用（高度可重用），而 Item 组件基本上是 List 组件中不可重用的部分。

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('SearchForm', () => {
  const searchFormProps = {
    searchTerm: 'React',
    onSearchInput: jest.fn(),
    onSearchSubmit: jest.fn(),
  };

  ...

# leanpub-start-insert
  it('changes the input field', () => {
    const pseudoEvent = { target: 'Redux' };

    component.root.findByType('input').props.onChange(pseudoEvent);

    expect(searchFormProps.onSearchInput).toHaveBeenCalledTimes(1);
    expect(searchFormProps.onSearchInput).toHaveBeenCalledWith(
      pseudoEvent
    );
  });

  it('submits the form', () => {
    const pseudoEvent = {};

    component.root.findByType('form').props.onSubmit(pseudoEvent);

    expect(searchFormProps.onSearchSubmit).toHaveBeenCalledTimes(1);
    expect(searchFormProps.onSearchSubmit).toHaveBeenCalledWith(
      pseudoEvent
    );
  });
# leanpub-end-insert
});
~~~~~~~

和 Item 组件一样，最后两个测试也是对组件的回调处理函数进行了测试。对于 SearchForm 组件的接口和与 InputWithLabel 组件的集成来讲，所有的输入（非函数 props）和输出（回调处理函数）props 都被测试了。

你也可以测试像禁用按钮这样的边界情况。在已经渲染的测试组件中提供了一个 `update()` 方法，它可以帮助我们在特殊情况下为组件提供新的 props。

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('SearchForm', () => {
  const searchFormProps = {
    searchTerm: 'React',
    onSearchInput: jest.fn(),
    onSearchSubmit: jest.fn(),
  };

  let component;

  beforeEach(() => {
    component = renderer.create(<SearchForm {...searchFormProps} />);
  });

  ...

# leanpub-start-insert
  it('disables the button and prevents submit', () => {
    component.update(
      <SearchForm {...searchFormProps} searchTerm="" />
    );

    expect(
      component.root.findByType('button').props.disabled
    ).toBeTruthy();
  });
# leanpub-end-insert
});
~~~~~~~

现在，我们将在应用程序的组件层次结构中再次深入。App 组件获取 list 数据后，会将其提供给 List 组件。在测试中引入 App 组件后，一个本地的测试会是这样：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
# leanpub-start-insert
describe('App', () => {
  it('succeeds fetching data with a list', () => {
    const list = [
      {
        title: 'React',
        url: 'https://reactjs.org/',
        author: 'Jordan Walke',
        num_comments: 3,
        points: 4,
        objectID: 0,
      },
      {
        title: 'Redux',
        url: 'https://redux.js.org/',
        author: 'Dan Abramov, Andrew Clark',
        num_comments: 2,
        points: 5,
        objectID: 1,
      },
    ];

    const component = renderer.create(<App />);

    expect(component.root.findByType(List).props.list).toEqual(list);
  });
});
# leanpub-end-insert
~~~~~~~

在实际的 App 组件中，我们使用第三方库（axios）向一个远程 API 发出请求。这个 API 返回的数据是我们在测试中无法预知的，所以我们必须对其进行 mock。Jest 提供了 mock 整个库及其方法的机制。在本例中，我们要模拟 axios 的 `get()` 方法来返回我们想要的数据。

{title="src/App.test.js",lang="javascript"}

~~~~~~~
import React from 'react';
import renderer from 'react-test-renderer';
# leanpub-start-insert
import axios from 'axios';
# leanpub-end-insert

# leanpub-start-insert
jest.mock('axios');
# leanpub-end-insert

...

describe('App', () => {
  it('succeeds fetching data with a list', () => {
    const list = [ ... ];

# leanpub-start-insert
    const promise = Promise.resolve({
      data: {
        hits: list,
      },
    });
# leanpub-end-insert

# leanpub-start-insert
    axios.get.mockImplementationOnce(() => promise);
# leanpub-end-insert

    const component = renderer.create(<App />);

    expect(component.root.findByType(List).props.list).toEqual(list);
  });
});
~~~~~~~

测试是同步读取的，但我们还是要处理异步数据。当组件的状态更新时，组件应该重新渲染。我们可以用我们的工具库和 async/await 来做到：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('App', () => {
# leanpub-start-insert
  it('succeeds fetching data with a list', async () => {
# leanpub-end-insert
    const list = [ ... ];

    const promise = Promise.resolve({
      data: {
        hits: list,
      },
    });

    axios.get.mockImplementationOnce(() => promise);

# leanpub-start-insert
    let component;

    await renderer.act(async () => {
      component = renderer.create(<App />);
    });
# leanpub-end-insert

    expect(component.root.findByType(List).props.list).toEqual(list);
  });
});
~~~~~~~

我们并没有渲染 App 组件，而是通过 mock 获取数据的方法来模拟远程 API 的响应。为了专注于 happy path，我们告诉测试将组件视为异步更新组件。你可以将类似的策略应用到 unhappy path 上：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('App', () => {
  it('succeeds fetching data with a list', async () => {
    ...
  });

# leanpub-start-insert
  it('fails fetching data with a list', async () => {
    const promise = Promise.reject();

    axios.get.mockImplementationOnce(() => promise);

    let component;

    await renderer.act(async () => {
      component = renderer.create(<App />);
    });

    expect(component.root.findByType('p').props.children).toEqual(
      'Something went wrong ...'
    );
  });
# leanpub-end-insert
});
~~~~~~~

现在数据的获取和与远程 API 的集成已经测试完毕。我们从专注于单个组件的单元测试，转向了对多个组件的测试，以及它们与第三方如 axios 和远程 API 的集成。

### 快照测试

Jest 还可以让你对你的渲染组件进行**快照**，将其与未来抓取的快照结果进行对比，并在发现变动时通知你。然后根据所需的结果，可以接受或拒绝更改。这种机制是对单元和集成测试的很好补充，因为它只测试渲染输出的差异，而不需要大量的维护成本。要查看它的实际操作，请用你的第一个快照测试来扩展 Item 组件的测试套件：

{title="src/App.test.js",lang="javascript"}

~~~~~~~
describe('Item', () => {
  ...

  beforeEach(() => {
    component = renderer.create(
      <Item item={item} onRemoveItem={handleRemoveItem} />
    );
  });

  ...

# leanpub-start-insert
  test('renders snapshot', () => {
    let tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });
# leanpub-end-insert
});
~~~~~~~

再次运行你的测试，观察它们如何成功或失败的。一旦我们更改 *src/App.js* 文件中 Item 组件的渲染块的输出，由此改变了返回的 HTML 结构，快照测试就会失败。这时，我们就可以决定是更新快照还是对 Item 组件进行调查。

Jest 将快照存储在一个文件夹中，这样它就可以对未来的快照测试进行差别验证。用户可以跨团队共享这些快照以进行版本控制（例如：git）。第一次运行快照测试时，会在项目文件夹中创建快照文件。当再次运行测试时，快照按照预期将与最近一次测试运行的版本相匹配。这就是我们如何确保 DOM 保持不变的方法。

### 练习

* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing)。
  * 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/react-testing?expand=1)。
* 为所有其他的组件加上一个快照测试。
* 阅读更多关于[测试 React 组件](https://www.robinwieruch.de/react-testing-tutorial)的信息。

  * 阅读更多关于 [Jest](https://jestjs.io/) 和 [React 中的 Jest](https://www.robinwieruch.de/react-testing-jest/) 用于单元测试、集成测试和快照测试的信息。
* 阅读更多关于 [React 中端到端测试](https://www.robinwieruch.de/react-testing-cypress)的信息。
* 在接下来的章节中继续学习课程的同时，保持你的测试结果为绿色，并且在你任何时候觉得应该加测试的时候加上新的测试。
