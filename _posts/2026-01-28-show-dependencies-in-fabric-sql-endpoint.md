---
layout: post
title: Viewing SQL object dependencies in Fabric
categories: [Fabric Warehouse, Fabric Lakehouse, Fabric SQL Endpoint, SQL, Fabric, SSMS, SQL Analytics Endpoints]
---

Every now and then I have to check object dependencies in a "data store". For "old style" SQL databases this can be easily achived via SQL Server Management Studio (SSMS) with the option of "View Dependencies" in the context menu of the object you are intrested in - easy peasy...

[![ContextMenuSQLDB](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/ContextMenuSQLDB.jpg "view dependencies option in context menu for a SQL database")](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/ContextMenuSQLDB.jpg){:.glightbox}

If you are connected to a Fabric Warehouse/SQL Analytics Endpoint the context menu does look a little bit different... 

[![ContextMenuSQLEndpoint](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/ContextMenuSQLEndpoint.jpg "view dependencies option in context menu for a SQL Endpoint (in Fabric)")](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/ContextMenuSQLEndpoint.jpg){:.glightbox}

## What to do now...

Luckily there are some objects which can be queried to track how objects are related.

### INFORMATION_SCHEMA.VIEW_TABLE_USAGE
Shows all views and the objects (views and tables) they are reference to. But only if those objects reside within the same SQL Analytics Endpoint. If you just want to know which objects are used by a specific view and are sure that all objects exists in the same database (means no shortcuts) this is the simplest method.

[![ViewTableUsageOutput](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/ViewTableUsageOutput.jpg "Output of INFORMATION_SCHEMA.VIEW_TABLE_USAGE")](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/ViewTableUsageOutput.jpg){:.glightbox}

### INFORMATION_SCHEMA.VIEW_COLUMN_USAGE
Lists the specific columns used within view definitions. This allows a more granular look at column-level usage, but with the same restrictions as for VIEW_TABLE_USAGE

[![ViewColumnUsageOutput](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/ViewColumnUsageOutput.jpg "Output of INFORMATION_SCHEMA.VIEW_COLUMN_USAGE")](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/ViewColumnUsageOutput.jpg){:.glightbox}

### sys.sql_expression_dependencies
Provides a broader picture of dependency relationships. It includes cross‑database references and even includes stored procedures. 
Column-level details are only available for schema-bound objects (those created with WITH SCHEMABINDING), but the view is still one of the most complete sources in Fabric. 

[![sysexpressiondependenciesOutput](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/sysexpressiondependenciesOutput.jpg "Output of sys.sql_expression_dependencies")](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/sysexpressiondependenciesOutput.jpg){:.glightbox}

## My go-to-query

Based on sys.sql_expression_dependencies in combination with sys.objects I created a query which provides me with the most relevant informations, especially in which "database" resides the referenced project.

```SQL
SELECT
	DB_NAME() ReferencingDB,
	OBJECT_SCHEMA_NAME(referencing_id) ReferencingSchema,
	OBJECT_NAME(referencing_id) ReferencingObject,
	DB_NAME()+'.'+OBJECT_SCHEMA_NAME(referencing_id)+'.'+OBJECT_NAME(referencing_id) AS ReferencingObjectFullName,
	ReferencingObj.type_desc AS ReferencingObjectType,
	COALESCE(referenced_database_name, DB_NAME()) AS ReferencedDB,
	referenced_schema_name AS ReferencedSchema,
	referenced_entity_name AS ReferencedObject,
	COALESCE(referenced_database_name, DB_NAME())+'.'+referenced_schema_name+'.'+referenced_entity_name AS ReferencedObjectFullName,
	COALESCE(ReferencedObj.type_desc, 'External Object') AS ReferencedObjectType
FROM sys.sql_expression_dependencies dep
INNER JOIN sys.objects ReferencingObj ON
	dep.referencing_id = ReferencingObj.object_id
LEFT JOIN sys.objects ReferencedObj ON 
	dep.referenced_id = ReferencedObj.object_id
--WHERE OBJECT_NAME(referencing_id) = '[PUT YOUR OBJECT NAME HERE]'
;
```
Whenever I need to get an overview about SQL Object lineage in a Fabric environment this is my first step to insight. 

[![QueryOutput](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/QueryOutput.jpg "Output of my go-to query")](https://gregorprohaska.github.io/assets/image/blogpictures/2026-01-28-show-dependencies-in-fabric-sql-endpoint/QueryOutput.jpg){:.glightbox}

### Issues\Disclaimer
* I haven't tested this query with schema bound objects, therefore no columns/column names are included. Most probably you will see multiple lines for same referencing/referenced-combination.
* ObjectTypes are only defined for objects within the database
* Even though queryinsights objects resides within the same database those objects are not recognized by sys.objects
* VIEW_TABLE_USAGE and VIEW_COLUMN_USAGE do **not** show objects if they **reference only objects from other databases**. Think about DB1.View1 --> DB1.View2 --> DB2.Table1, no reference to DB1.View2 is shown! (Take a closer look at the screenshots ;-)