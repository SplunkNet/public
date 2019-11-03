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
// Equals (same as not including the 3rd parameter), not equals, greater than, less than 
incidentGR.addQuery('priority', '=', 2);
incidentGR.addQuery('priority', '!=', 2);
incidentGR.addQuery('priority', '>', 2);
incidentGR.addQuery('priority', '<', 2);
incidentGR.addQuery('priority', '>=', 2);
incidentGR.addQuery('priority', '<=', 2);

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
// Order by field ascending/descending
incidentGR.orderBy('number');
incidentGR.orderByDesc('sys_created_on');

// Find all active/incative records
incidentGR.addActiveQuery();
incidentGR.addInactiveQuery();

// Find all records where the field is empty / not empty
incidentGR.addNullQuery('short_description');
incidentGR.addNotNullQuery('short_description');

// Limit the number or records returned
incidentGR.setLimit(5);

// Explain
incidentGR.chooseWindow(10, 20);
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

### Get all field details for a GlideRecord  
```javascript
function getAllFields(glideRec) {
   var prop;
   var props = [];
   for (prop in glideRec) {
      props.push(prop);
   }
   props.sort();

   var fieldsObject = {};
   for (var i = 0; i < props.length; i++) {
      var fieldValue = glideRec.getValue(props[i]);
      var fieldDisplayValue = glideRec.getDisplayValue(props[i]);
      if (fieldValue && fieldValue !== fieldDisplayValue) {
         fieldsObject[props[i]] = {value: fieldValue, display_value: fieldDisplayValue};
      } else {
         fieldsObject[props[i]] = fieldValue;
      }
   }

   var fieldsData = JSON.stringify(fieldsObject, null, 3);
   gs.info('\n' + fieldsData);
}

var incidentGR = new GlideRecord('incident');
if (incidentGR.get('number', 'INC0000059')) {
   getAllFields(incidentGR);  
}
```
