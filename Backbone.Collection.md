## Backbone.Collection ##    
集合是模型的有序组合。你可以在集合上绑定`change`事件，当集合中的模型发生变化时得到通知，你也可以监听`"add"`和`"remove"`事件，更新服务器，并能使用Underscore.js提供的方法。   
集合中的模型触发的任何事件都可以在集合身上直接触发，为了方便，你可以监听集合中任何模型的指定属性变化：比如`document.on("change:selected", ...)`       
**extend** `Backbone.Collection.extend(properties, [classProperties])`   

**model** `collection.model`    

**constructor/initialize** `new Collection([models], [options])`   

**models** `collection.models`   

**toJSON** `collection.toJSON()`   

**sync** `collection.sync(method, collection, [options])`    

### Underscore Methods(28) ###    
Underscore.js提供28个迭代函数。   


**add** `collection.add(models, [options])`   
向集合添加一个模型或模型数组，会触发`"add"`事件。   

**remove** `collection.remove(models, [options])`  

**reset** `collection.reset([models], [options])`  

