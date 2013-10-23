## Backbone.Events ##
**Events**是可以合并到任何对象上的模块，合并后的这些对象拥有绑定和触发自定义事件的功能。Events不需要在绑定之前就要声明，并且可以向事件处理程序传递参数。例如：
    
    var object = {};

	_.extend(object, Backbone.Events);
	object.on('alert', function(msg){ 
		alert('Triggered ' + msg); 
	});
	object.trigger('alert', 'an events');
**on** `object.on(event, callback, [context])`  *Alias: bind*   
绑定回调函数`callback`到对象`object`.当事件被触发时执行绑定的`callback`。如果页面中有大量的事件，按照惯例使用冒号制定命名空间：`poll:start`或`change:selection`。事件字符串也可以由几个空格分隔的事件组成。
> book.on("change:title change:author", ...);

当回调函数执行时提供的第三个参数，可以为this指定上下文：`model.on('change', this.render, this)`   
任何事件发生时，都会执行绑定到`all`事件上的回调函数，且第一个参数为事件的名称。例如，将一个对象的所有事件代理到另一个对象：  
    
    proxy.bind('all', function(eventName){
        object.trigger(eventName);
    });

所有的事件方法也都支持事件的语法映射，作为可选的定制参数:

    book.on({
        "change:title": titleView.update,
        "change:author": authorPane.update
    });
 

