英文原文：[http://emberjs.com/guides/testing/testing-routes/](http://emberjs.com/guides/testing/testing-routes/)

_单元测试方案和计算属性与之前[单元测试基础]中说明的相同，因为`Ember.Route`集成自`Ember.Object`。_

路由测试可以通过集成测试或者单元测试来进行。集成测试对路由的测试具有更好地覆盖性，因为路由通常用来执行过渡和数据加载，这些测试在完整上下文中更加容易测试，而独立上下文则没有那么容易。

不过有时候路由的单元测试也是非常重要的。例如，希望应用可以在任意地方都能触发一个警告。警告函数`displayAlert`应该被定义在`ApplicationRoute`中，因为所有操作或者事件都会从子路由、控制器和视图冒泡到该路由。

```javascript
App.ApplicationRoute = Em.Route.extend({
  actions: {
    displayAlert: function(text) {
      this._displayAlert(text);
    }
  },

  _displayAlert: function(text) {
    alert(text);
  }
});
```

这使得使用`moduleFor`成为了可能。

在这个路由中，首先[分离关注点](http://en.wikipedia.org/wiki/Separation_of_concerns)：操作`displayAlert`包含了在收到操作时将调用的代码，而私有方法`_displayAlert`执行操作。当然可能这里代码这么简单，没有必要这么设计，但是将关注点进行分离，可以使得对于测试来说具有更好地可读性，这也有利于发现漏洞。

下面是如何对路由进行测试的示例：

```javascript

moduleFor('route:application', 'Unit: route/application', {
  setup: function() {
    originalAlert = window.alert; // store a reference to the window.alert
  },
  teardown: function() {
    window.alert = originalAlert; // restore original functions
  }
});

test('Alert is called on displayAlert', function() {
  expect(1);

  // with moduleFor, the subject returns an instance of the route
  var route = this.subject(),
      expectedText = 'foo';

  // stub window.alert to perform a qunit test
  window.alert = function(text) {
    equal(text, expectedText, 'expected ' + text + ' to be ' + expectedText);
  }

  // call the _displayAlert function which triggers the qunit test above
  route._displayAlert(expectedText);
});
```

#### 在线示例

<a class="jsbin-embed" href="http://jsbin.com/xivoy/embed?output">自定义测试助手</a>

<script src="http://static.jsbin.com/js/embed.js"></script>

[单元测试基础]: /guides/testing/unit-testing-basics
[分离关注点]: http://en.wikipedia.org/wiki/Separation_of_concerns
