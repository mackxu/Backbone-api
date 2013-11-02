## Backbone.Model ##
**Models** 

下面是简单的实例，但它展示了模型可以自定义方法，设置属性和触发一个指定属性的`change`事件.这段代码执行后，`sidebar`变量就可以用了。

	var Sidebar = Backbone.Model.extend({
		promptColor: function(){
			var cssColor = prompt('Please enter a CSS color: ');
			this.set({color: cssColor});
		}
	});
	
	window.sidebar = new Sidebar;
	sidebar.on('change:color', function(model, color){
		$('#sidebar').css({background: color});
	});

	sidebar.set({ color: 'white' });
	sidebar.promptColor();

**extend**  `Backbone.Model.extend(properties, [classProperties])`   
定义自己的`Model`类，可以通过参数提供的实例属性**properties**和可选的类属性**classProperties**来扩展`Backbone.Model`  
**extend**其实是创建一个原型链，因此通过**extend**创建的子类还可以继续派生子类。

> JavaScript没有提供简单的方法访问父类`super`.如果你重写了这些函数`set`或`save`，但希望调用父类对象的这些接口，你可以通过下面的方式：

	var Note = Backbone.Model.extend({
		set: function(attributes, options){
			Backbone.Model.prototype.set.apply(this, arguments);
			...
		}
	});

**constructor/initialize**  `new Model([attribute], [options])`   
如果你定义了*initialize*，那么每次实例化模型对象时都会被调用。

如果你感兴趣，可以重载此方法，它允许这样的替换

	var Library = Backbone.Model.extend({
		constructor: function(){
			this.books = new Books();
			Backbone.Model.apply(this, arguments);
		},
		parse: function(data, options){
			this.books.reset(data.books);
			return data.library;
		}
	});

如果你传递`{'collection':...}`作为**options**,那么模型会拥有`collection`属性，用来指明模型属于哪个集合和计算模型的`url`，否则，当你把模型添加到集合中是，会自动把`collection`属性添加到模型中。

`{url: '...'}`或者`{urlRoot: '...'}`作为参数时，needs to have a custom one-off URL endpoint

如果**option**中定义了`{parse: true}`,在模型设置属性的值之前，会先调用`parse`方法

**get**  `model.get(attribute)`   
获取当前属性的值   
**set**  `model.set(attributes, [options])`  
为模型设置哈希属性值，如果模型中的属性中的值被更改，会在模型上触发`"change"`事件，如果你也这样绑定了属性事件：`change:title`和`change:content`，此时也会触发。你也可以单独设置一个属性。  
**escape** `model.escape(attribute)`  
与**get**相同，获取属性值。防XSS攻击  
**has**  `model.has(attribute)`  
检测属性是否已经存在.如果属性不是null或者没有定义，则返回`true`  
**unset**	`model.unset(attribute, [options])`  
从属性哈希表中删除一个属性，除非传递`silent`参数，否则会触发`change`事件   
**clear**  `model.clear([options])`  
删除模型的所有属性，包括`id`属性。会触发`change`事件，除了传递`silent`参数。   
**id** `model.id`  
模型的一个特殊属性，id的值是一个arbitrary字符串（数字id或UUID）。如果你在属性表中设置了id，那么它会被直接作为模型的属性。通过id可以直接从集合中获取模型，并且它常用来生成模型的URLs  
**cid** `model.cid`  

**attributes**  `model.attributes`  

**defaults** `model.defaults`或`model.defaults()`  
`defaults`哈希对象或函数用来指定属性的默认值，当创建模型实例时，任何未指定的属性都会设置为默认值。
> 值得注意的是：在JavaScript中，对象通过引用传递。因此如果你的defaults是对象，它会被所有实例共享。相反，它的值定义成方法就不一样了。

**toJSON** `model.toJSON`  
返回JSON格式的所有模型属性的拷贝。它在被发送给服务器之前可以用来持久化，序列化或者augmentation。方法的名字会令人困惑，由于实际上它返回的不是JSON字符串——这个工作可以通过`JSON.stringify`实现

	var artist = new Backbone.Model({
		firstName: 'Wassily',
		lastName: 'Kandinsky'
	});
	
	alert(JSON.stringify(artist));   
**sync**  `model.sync(method, model, [options])`  
利用`Backbone。sync`传递模型状态到服务器。它还可被自定义重载。  
**fetch**  `model.fetch([options])`  
通过`Backbone.sync`代理，从服务器中获取数据更新模型状态，返回`jqXHR`对象。这些情况下是有用的，如果模型never been populated with data 或者你想去人人模型的数据是最新的服务器端数据。如果从服务器获取的数据和当前模型状态不同，则会触发`change`事件。参数列表中接收`success`和`error`回调函数，每个回调函数都会传递`(model, response, options)`参数。  
**save** `model.save([attributes], [options])`  
通过委托`Backbone.sync`保存模型到数据库（或者持久化层），如果成功返回一个`jqXHR`对象，否则返回`false`。属性*attribute*应该包含你要改变的属性——那些没被改变的不应该在此。当然，发送给服务器端的是一个完整的资源。和`set`一样，你可以传递单个属性和其值，而不仅仅是哈希表。如果模型有`validate`方法，如果验证失败，模型将保存不成功。如果模型是`isNew`，方法会是个`create`（HTTP`POST`）请求，如果模型已经存在，那么方法会是个`update`（HTTP`PUT`）请求。  
当然你可能只想要被改变的属性被发送到服务器端，利用`model.save(attrs, {patch: true})`,你可以发送一个HTTP`PATCH`请求，传送的数据仅仅是参数`attributes`.  
执行`save`时有新的属性会触发`'change'`事件，当Ajax开始向服务器发送时会触发`'request'`事件，当请求成功完成时会触发`'sync'`事件。如果你想等到服务器响应后再更新模型，可通过传递`{wait: true}`   
在下面的例子中，注意我们怎样重载`Backbone.sync`，第一次发送的是·`'{create}'`请求，第二次发送的是`'update'`请求。  

	Backbone.sync = function(method, model){
		alert(method + ': ' + JSON.stringify(model));
		model.id = 1;
	}

	var book = new Backbone.Model({
		title: 'zhi qing chun',
		authod: 'xi li wu'
	});

	book.save();
	book.save({authod: 'Teddy'});

*save*接收`success`和`error`回调函数组成的参数哈希表，它们的参数分别是`(model, response, options)`和`(model, xhr, options)`。如果服务器响应失败，会返回非200的响应码，紧接着会执行`error`回调。
**destory** `model.destory([options])`  
通过委托`Backbone.sync`发送一个HTTP`DELETE`请求，从服务器端销毁一个模型。返回一个*jqXHR*对象，或者如果模型是`isNew`返回false。在可选参数表中，接收`success`和`error`回调函数，对应的传递的参数为`(model, response, options)`和`(model, xhr, options)`。模型上会触发`destroy`事件，此事件会冒泡到包含删除模型的集合上，当向服务器开始发送请求时，触发`request`事件，当服务器端响应完成时会触发`sync`事件。通过传递`{wait: true}`可以保证在把模型从集合中删除前，完成了服务器响应。

	book.destroy({success: function(model, response){
		...
	}});
### Underscore ###
**pick** `_.pick(object, [keys])`  
返回一个`object`副本，只过滤出`keys`参数指定的属性值。  
**omit** `_.omit(object, [keys])`  
返回一个`object`副本, 只过滤出除去`keys`参数指定的属性值   
**invert** `_.invert(object)`   
返回一个object副本，使其键（keys）和值（values）对换。对于这种操作，所有的对象的值必须是字符串。    
**pairs** `_.pairs(object)`  
转变对象为一个`[key, value]`对数组   

**validate** `model.validate(attributes, options)`  
该方法是未实现状态，你被鼓励定义自己的验证逻辑重载该方法。if you have any that can be performed in JavaScript.默认下，在执行`save`之前会被自动调用，如果传递是`{validate: true}`时，在执行`set`函数时也会被执行。*validate*方法接收的参数和`set`或`save`相同。如果验证通过，不会返回任何东西，如果失败，会返回你自定义的错误。它可能是简单的一个错误字符串，或者是个完整的错误对象。如果验证返回错误信息，那么`save`将不再执行，服务器端模型的属性值也不会变。验证失败会触发`invalid`事件，还会为模型设置属性`validationError`，其值是验证方法错误返回的值。

	var Chapter = Backbone.Model.extend({
		validate: function(attrs, options){
			if(attrs.end < attrs.start){
				return 'can't end before it starts';
			}
		}
	});
	var one = new Chapter({ title: 'Chapter One: The Beginning' });
	one.on('invalid', function(model, error){
		alert(model.get('title')+ ' ' + error);
	});
	one.save({ start: 15, end: 10 });

`invalid`事件是有用的在提供coarse-grained error messages at the model or collection level.         
**validationError** `model.validationError`   
其值是最新验证错误   
**isValid** `model.isValid`  
执行validate()检查模型的状态。   

	if(!one.isValid()){
		alert(one.get('title')+ ' '+one.validationError);
	}  
**url** `model.url()`  
返回模型在服务器端的URL。如果你的模型在其他位置，可以重载这个方法。默认情况下，URLs由`"[collection.url]/[id]"`生成，但如果不考虑模型的集合的话，你由明确的模型的属性`urlRoot`来生成。   
如果所有的模型都采用普通的URL，那么一个模型的id`101`和`Backbone。Collection`的url`/documents/7/notes`会组合成这样：`/documents/7/notes/101`。                      
**urlRoot** `model.urlRoot`或`model.urlRoot()`   
如果你outside of collection 用一个模型，为了确保*url*方法能根据模型的id默认生成URLs，可以指定属性`urlRoot`。一般很少需要定义该属性的，注意，`urlRoot`也可以定义为一个方法。      
**parse** `model.parse(response, options)`  
每当从服务器端获取模型数据时都会自动调用*parse*方法，例如在*fetch*和*save*。  
**clone** `model.clone()`  
返回一个属性完全相同的模型实例。   
**isNew** `model.isNew()`  
该模型是在服务器端存储过？如果这个模型还没有属性`id`，那么它会被认为是新建的。   
**hasChanged** `model.hasChanged([attribute])`  
如果指定的属性在`"change"`事件中被更改了返回`true`       
**changedAttributes** `model.changedAttributes([attributes])`   
返回模型更新的属性，如果没有返回`false`。通过传递*attributes*可以指定返回的属性。    
**previous** `model.previous(attribute)`  
在一个`"change"`事件处理程序中，该方法返回被改变属性之前的值。

	var bill = new Backbone.Model({
		name: 'Bill Smith'
	});
	bill.on('change:name', function(model, name) {
		alert('Changed name from '+bill.previous('name')+' to ' + name);
	});
	bill.set({ name: 'Bill Jones' });   
**previousAttributes** `model.previousAttributes()`   
获取模型之前属性的副本。用来获取模型上下版本的不同，或者在错误后获取之间的合法数据。
  















