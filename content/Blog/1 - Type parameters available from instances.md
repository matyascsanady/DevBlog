---
tags:
  - Revit
  - API
  - CSharp
  - FeatureOrBug
---
# The Problem
Have you ever tried to check whether a Revit [built-in parameter](https://apidocs.co/apps/revit/2024/fb011c91-be7e-f737-28c7-3f1e1917a0e0.htm) is an instance or a type parameter by trying to get it from an element and examining the results? Let's say at runtime you have a built-in parameter, which the user gave or selected. At his point you don't know if it's an instance or a type parameter. An experienced developer would try to get this parameter from an element and examine the result, say i tried to get this parameter of a type and got back a null, than i know that this is an instance parameter (assuming that the category of the element is such that is has this parameter).

How does it look like in C#?
```cs
Parameter param = element.get_Parameter(BuiltInParameter.ALL_MODEL_INSTANCE_COMMENTS)
```
If the Parameter element is `null`, we now that this built in parameter is an instance parameter. Of course in this case the name of it was helpful as well, but since the naming of this enumerator is unreliable.

Now this is all nice and well until this point, but dear Revit is trying to be helpful and making some exclusions...

There are some type built-in that are returned from an instance as well as from a type. So far I have identified 3 of these:
- *"Assembly Code"*
- *"Assembly Description"*
- *"Type IfcGUID"*

# The Solution
Well sadly this isn't really a solution more like a band aid. You can create a static blacklist of these parameters, and if the parameter you are trying to get matches any from the blacklist you skip that. Or in plain C#:
```cs
// define a blacklist
private readonly List<BuiltInParameter> _blacklist = [
	BuiltInParameter.UNIFORMAT_CODE,
	BuiltInParameter.UNIFORMAT_DESCRIPTION,
	BuiltInParameter.IFC_TYPE_GUID
	];

// later

// check if it's in the blacklist
if (_blacklist.Contains(builtInParameter))
{
	continue;
}
```
I know it isn't nice or elegant or whatever, but it works.