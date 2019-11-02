### GlideRecord query
```javascript
var incidentGR = new GlideRecord('incident');
incidentGR.addQuery('active', true);
incidentGR.addQuery('category', 'hardware');
incidentGR.addQuery('caller_id.name', 'Rick Berzle');
incidentGR.query();
while (incidentGR.next()) {
   gs.info(incidentGR.getValue('number'));
}
```
### .get()
```javascript
// get by sys_id
var incidentGR = new GlideRecord('incident');
if (incidentGR.get('85071a1347c12200e0ef563dbb9a71c1')) {
   gs.info(incidentGR.getValue('number'));   
}

// get by any field
var incidentGR = new GlideRecord('incident');
if (incidentGR.get('number', 'INC0000059')) {
   gs.info(incidentGR.getDisplayValue('caller_id'));   
}

// with dot-walking
var incidentGR = new GlideRecord('incident');
if (incidentGR.get('caller_id.name', 'Rick Berzle')) {
   gs.info(incidentGR.getValue('number'));   
}
```

### Insert
```javascript
var incidentGR = new GlideRecord('incident');
incidentGR.initialize();
incidentGR.setValue('active', true);
incidentGR.setValue('priority', 2);
incidentGR.setValue('description', 'test');
incidentGR.insert();

// Get the sys_id of the newly created record
var newSysId = incidentGR.insert();
```

### Delete
```javascript
var incidentGR = new GlideRecord('incident');
incidentGR.addQuery('active', true);
incidentGR.addQuery('number', 'INC0000059');
incidentGR.query();
while (incidentGR.next()) {
   incidentGR.deleteRecord();
}
```

### Query operators
```javascript
incidentGR.addQuery('priority', '=', 2);  // Equals (same as not including the 3rd parameter)
incidentGR.addQuery('priority', '>', 2);  // Greater than
incidentGR.addQuery('priority', '<', 2);  // Less than
incidentGR.addQuery('priority', '>=', 2); // Greater than or equals
incidentGR.addQuery('priority', '<=', 2); // Less than or equals
incidentGR.addQuery('priority', '!=', 2); // Not equals
 
incidentGR.addQuery('short_description', 'STARTSWITH', 'Printer');
incidentGR.addQuery('short_description', 'ENDSWITH', 'Printer');
incidentGR.addQuery('short_description', 'CONTAINS', 'Printer');
incidentGR.addQuery('short_description', 'DOES NOT CONTAIN', 'Printer');
 
// IN & NOT IN for lists
incidentGR.addQuery('sys_id', 'IN', '8d641046c0a80164000bc7c0d3ed46a0,a9a16740c61122760004fe9095b7ddca');
incidentGR.addQuery('sys_id', 'NOT IN', '8d641046c0a80164000bc7c0d3ed46a0,a9a16740c61122760004fe9095b7ddca');

//Return only records that are instances of an extended table (like incident is of task in this example)
incidentGR.addQuery('sys_class_name', 'INSTANCEOF', 'incident');
```

### Other parameters
```javascript
// Order by
incidentGR.orderBy('category');

// Order by descending
incidentGR.orderByDesc('sys_created_on');

// Limit the number or records returned
incidentGR.setLimit(5);

//
incidentGR.chooseWindow(10, 20);

// Find all records where the Short Description is empty
incidentGR.addNullQuery('short_description');

// Find all records where the Short Description is NOT empty
incidentGR.addNotNullQuery('short_description');

// Find all active incidents, equal to: incidentGR.addQuery('active', true);
incidentGR.addActiveQuery();

// Find all inactive incidents, equal to: incidentGR.addQuery('active', false);
incidentGR.addInactiveQuery();
```

### OR query
```javascript
//Find all incidents with a priority of 1 or 2
var incidentGR = new GlideRecord('incident');
incidentGR.addQuery('priority', 1).addOrCondition('priority', 2);
incidentGR.query();
while (incidentGR.next()) {
   //Do something with the records returned
}

//Alternative to find all incidents with a priority of 1 or 2
var incidentGR = new GlideRecord('incident');
var incidentGROR = incidentGR.addQuery('priority', 1);
incidentGROR.addOrCondition('priority', 2);
incidentGR.query();
while (incidentGR.next()) {
   //Do something with the records returned
}
```

### .addEncodedQuery()
```javascript
//Find all active incidents where the category is software or hardware
var myEncodedQuery = 'active=true^category=software^ORcategory=hardware'
var incidentGR = new GlideRecord('incident');
incidentGR.addEncodedQuery(myEncodedQuery);
incidentGR.query();
while (incidentGR.next()) {
   //Do something with the records returned
}

// Alternatively, you can break down the encoded query
var strQuery = 'active=true';
strQuery = strQuery + '^category=software';
strQuery = strQuery + '^ORcategory=hardware';

// CANNOT be used in Client scripts and UI policies! Use ‘addQuery(YOURENCODEDQUERYHERE)’ instead.
```

### .getDisplayValue()  
```javascript
var incidentGR = new GlideRecord('incident');
if (incidentGR.get('number', 'INC0000059')) {
   gs.info(incidentGR.getDisplayValue('caller_id'));   
} else {
   gs.info('Not found!');
}
```
