# Revit

## Common Operations

### Selecting Elements

The general workflow for dealing with user selections in a Revit plugin is:

* Define a [Selection filter](concepts.md#iselectionfilter-interface) class.
* Initialize a [Reference](concepts.md#reference) object to accept the picked result.
* Initialize a *selection filter* object from your defined class.
* Initialize a [Selection](https://www.revitapidocs.com/2024/31b73d46-7d67-5dbb-4dad-80aa597c9afc.htm) object.
* Call one of the [`Pick...` methods](https://www.revitapidocs.com/2024/8eccaa93-cc99-fd37-15ad-24d201985d9b.htm) on the Selection object and assign the result to the reference object.
    * Note: It is at this stage that you will pass the *Selection Filter* object as an argument.
* Get the [Element](concepts.md#element) from the reference using `doc.GetElement(reference)`
* Check if the *element* is null.
* Do stuff with your selection.

For example:

```csharp

// Filter object set up elsewhere in code

// Initialize a reference object to accept the pick result
Reference signRef = null;

// Initialize a Filter Object
FilterNamedFamily signFilter = new FilterNamedFamily("wall_based");

// Initialize a Selection Object
Selection signSel = uiApp.ActiveUIDocument.Selection;

// Call a pick method on the selection object. Assign result to ref object.
signRef = signSel.PickObject(ObjectType.Element, signFilter, "Pick a sign family!");

// Get the element from the reference
Element signElem = doc.GetElement(signRef);

// Cast element as familyinstance
FamilyInstance family = signElem as FamilyInstance;
if (family == null)
{
    return Result.Failed; // just in case
}

// do stuff here!
```
