## Backbone.Events ##
**Events**是可以合并到任何对象上的模块，合并后的这些对象拥有绑定和触发自定义事件的功能。Events不需要在绑定之前就要声明，并且可以向事件处理程序传递参数。例如：
    
    var object = {};

	_.extend(object, Backbone.Events);
	object.on('alert', function(msg){ 
		alert('Triggered ' + msg); 
	});
	object.trigger('alert', 'an events');
**on** `object.on(event, callback, [context])`  *Alias: bind*   
 

