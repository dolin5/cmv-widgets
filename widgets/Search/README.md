#Search Widget for CMV
Used in conjunction with the [Attributes Table](https://github.com/tmcgee/cmv-widgets#attributes-tables) widget to provide a user interface for querying feature layers, dynamic layers, tables and related records using QueryTask and FindTask.

---
## Example Configuration:
``` javascript
search: {
    include: true,
    id: 'search',
    type: 'titlePane',
    path: 'widgets/Search',
    canFloat: true,
    title: 'Search',
    open: true,
    position: 0,
    options: 'config/searchWidget'
},
```

## Screenshot:
![Screenshot](https://tmcgee.github.io/cmv-widgets/images/search1.jpg)

---
## Example Search Widget Configuration:
``` javascript
define([
    'dojo/on',
    'dojo/date/locale'
], function (on, locale) {

    function formatDateTime (value) {
        var date = new Date(value);
        return locale.format(date, {
            formatLength: 'short'
        });
    }

    return {
        map: true,
        mapClickMode: true,

        /*
           Show button to open the Query Builder widget
           This new widget not yet been released
        */
        enableQueryBuilder: false,
        
        /*
            continue adding multiple shapes before searching
        */
        enableDrawMultipleShapes: true, 

        /*
            add the results of a search to the existing results from a previous search
        */
        enableAddToExistingResults: true,
        
        /*
            use spatial filters in searches by attribute
        */
        enableSpatialFilters: true,

        /*
            control which spatial filters are available
        */
        spatialFilters: {
            entireMap: true,
            currentExtent: true,
            identifiedFeature: true,
            searchFeatures: true,
            searchSelected: true,
            searchSource: true,
            searchBuffer: true
        },

        /*
            Control which drawing tools are available to the user
        */
        drawingOptions: {
            rectangle: true,
            circle: true,
            point: true,
            polyline: true,
            freehandPolyline: true,
            polygon: true,
            freehandPolygon: true,
            stopDrawing: true,
            identifiedFeature: true,
            selectedFeatures: true,

            // change the symbology for drawn shapes and buffer around them
            symbols: {}
        },

        layers: [
            {
                name: 'Damage Assessment',
                expression: '', // additional where expression applied to all queries
                idProperty: 'objectid',
                queryParameters: {
                    type: 'spatial', // spatial, relationship, table or database
                    layerID: 'DamageAssessment', // from operational layers
                    sublayerID: 0,
                    outFields: ['*']
                },
                attributeSearches: [
                    {
                        name: 'Search For Assessments',
                        searchFields: [
                            {
                                name: 'Inspector Name',
                                label: 'Inspector Name',
                                expression: '(inspector LIKE \'[value]%\')',
                                placeholder: 'Enter the name Fred',
                                required: true,
                                minChars: 3
                            },
                            {
                                name: 'Type of Damage',
                                label: 'Type of Damage',
                                expression: '(typdamage LIKE \'[value]%\')',
                                placeholder: 'Enter the text Destroyed, Major or Minor',
                                minChars: 3
                            }
                        ],

                        title: 'Assessments',
                        topicID: 'assessmentsQuery',
                        gridOptions: {
                            columns: [
                                {
                                    field: 'incidentnm',
                                    label: 'Name'
                                },
                                {
                                    field: 'inspdate',
                                    label: 'Inspected',
                                    width: 150,
                                    get: function (object) { // allow export as a proper date
                                        return new Date(object.inspdate);
                                    },
                                    formatter: formatDateTime
                                },
                                {
                                    field: 'inspector',
                                    label: 'Inspector'
                                },
                                {
                                    field: 'fulladdr',
                                    label: 'Address'
                                },
                                {
                                    field: 'pstlcity',
                                    label: 'City'
                                },
                                {
                                    field: 'typdamage',
                                    label: 'Damage'
                                },
                                {
                                    field: 'lastupdate',
                                    label: 'Updated',
                                    get: function (object) { // allow export as a proper date
                                        return new Date(object.lastupdate);
                                    },
                                    formatter: formatDateTime
                                }
                            ],
                            sort: [
                                {
                                    attribute: 'incidentnm',
                                    descending: 'ASC'
                                }
                            ]
                        }
                    }
                ]
            },
            {
                name: 'Hospitals',
                expression: '', // additional where expression applied to all queries
                idProperty: 'OBJECTID',
                queryParameters: {
                    type: 'table', // spatial, relationship, table or database
                    layerID: 'louisvillePubSafety', // from operational layers
                    sublayerID: 5,
                    outFields: ['*']
                },
                attributeSearches: [
                    {
                        name: 'Search For Hospital',
                        searchFields: [
                            {
                                name: 'Hospital Name',
                                label: 'Name',
                                expression: '(NAME LIKE \'[value]%\')',
                                placeholder: 'Enter the name of the hospital',
                                required: true,
                                minChars: 3
                            },
                            {
                                name: 'Total Admissions',
                                label: 'Total Admissions >=',
                                expression: '(TOTALADM >= [value])',
                                placeholder: 'Total Admissions >='
                            },
                            {
                                name: 'Total Admissions',
                                label: 'Total Admissions <=',
                                expression: '(TOTALADM <= [value])',
                                placeholder: 'Total Admissions <='
                            }
                        ],

                        title: 'Hospitals',
                        topicID: 'hospitalQuery',
                        gridOptions: {
                            columns: [
                                {
                                    id: 'Action',
                                    field: 'OBJECTID',
                                    label: 'Action',
                                    width: 32,
                                    sortable: false,
                                    exportable: false,
                                    renderCell: function (object, value, node) {
                                        on(node, 'click', function () {
                                            alert('Do something exciting here like search for related records or edit the selected record.');
                                        });
                                        node.innerHTML = '<i class=\'fa fa-pencil\' style=\'margin-left:8px;\'></i>';
                                    }
                                },
                                {
                                    field: 'NAME',
                                    label: 'Name',
                                    width: 150
                                },
                                {
                                    field: 'ADDRESS',
                                    label: 'Address',
                                    width: 150
                                },
                                {
                                    field: 'CITY',
                                    label: 'City',
                                    width: 80
                                },
                                {
                                    field: 'STABREV',
                                    label: 'State',
                                    width: 50
                                },
                                {
                                    field: 'ZIPCODE',
                                    label: 'Zip Code',
                                    width: 80
                                },
                                {
                                    field: 'TOTALADM',
                                    label: 'Total Admission',
                                    width: 100
                                },
                                {
                                    field: 'LASTUPDATE',
                                    label: 'Last Update',
                                    width: 100,
                                    get: function (object) { // allow export as a proper date
                                        return new Date(object.LASTUPDATE);
                                    },
                                    formatter: formatDateTime
                                }
                            ],
                            sort: [
                                {
                                    attribute: 'NAME',
                                    descending: 'ASC'
                                }
                            ]
                        }
                    },
                ]
            },
            {
                name: 'Police Stations',
                expression: '', // additional where expression applied to all queries
                queryParameters: {
                    type: 'table', // spatial, relationship, table or database
                    layerID: 'louisvillePubSafety', // from operational layers
                    sublayerID: 2,
                    outFields: ['*']
                },
                idProperty: 'OBJECTID',
                attributeSearches: [
                    {
                        name: 'Search For Police Station By Name',
                        searchFields: [
                            {
                                name: 'Police Station',
                                label: 'Name',
                                expression: '(PDNAME LIKE \'[value]%\')',
                                placeholder: 'Enter the Name of the Police Station',
                                required: true,
                                minChars: 3
                            }
                        ],

                        title: 'Police Stations',
                        topicID: 'policeStationQuery',
                        gridOptions: {
                            columns: [
                                {
                                    field: 'PDNAME',
                                    label: 'Name',
                                    width: 150
                                },
                                {
                                    field: 'ADDRESS',
                                    label: 'Address',
                                    width: 150
                                },
                                {
                                    field: 'PDTYPE',
                                    label: 'Type',
                                    width: 100
                                },
                                {
                                    field: 'FUNCTION',
                                    label: 'Function',
                                    width: 100
                                },
                                {
                                    field: 'LASTUPDATE',
                                    label: 'Last Update',
                                    width: 100,
                                    get: function (object) { // allow export as a proper date
                                        return new Date(object.LASTUPDATE);
                                    },
                                    formatter: formatDateTime
                                }
                            ],
                            sort: [
                                {
                                    attribute: 'PDNAME',
                                    descending: 'ASC'
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    };
});
```

## Screenshot:
![Screenshot](https://tmcgee.github.io/cmv-widgets/images/search2.jpg)

---
##Search Topics

### Subscribed Topics
The Search widget subscribes to the following topics. The topicID should be unique for each instance of the widget.
``` javascript
// execute a basic search  (incomplete and untested)
topicID + '/search'

// execute a query
topicID + '/executeQuery'

//  update the available spatial filters when the table (tab) is updated
this.attributesContainerID + '/tableUpdated'

// set the sql where clause for the current attributes search
this.topicID + '/setSQLWhereClause'

this.topicID + '/clearSQLWhereClause'

// listens for the mapClickMode changing
'mapClickMode/currentSet'
```

### Published Topics
The Search widdet publishes the following topics. The topicID should be unique for each instance of the widget.
```javascript
// publishes to Growl widget to provide users with information such as when a query is executing or details about the query results (number of results)
'growler/growl'

// publish a change in mapClickMode
'mapClickMode/setCurrent'

// return the  mapClickMode to the default
'mapClickMode/setDefault'

// publish to an accompanying attributes table and running the submitted query or find task. 
this.attributesContainerID + '/addTable'

// opens the QueryBuilder widget
this.queryBuilderTopicID + '/openDialog'
```