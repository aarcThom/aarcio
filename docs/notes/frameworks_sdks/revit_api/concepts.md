# Revit API Concepts

## E

### Element

An Element is the base object for nearly everything in a Revit project database. It represents a whole item, whether it's a physical part of the building, an annotation, or a project setting.

**What it Represents: A complete, individual component.**

* **Model Elements:** Walls, Doors, Windows, Beams, Floors.
* **Annotation Elements:** Dimensions, Text Notes, Tags.
* **Datum Elements:** Levels, Grids, Reference Planes.
* **Abstract/Type Elements:** Wall Types, Family Symbols, View Templates.

**Key Identifier:** Every element has a unique ElementId. This ID is used to retrieve the entire element from the Revit database.

**Analogy:** Think of an Element as the entire car in contrast to a *reference* which could be thought of as a component of a car.

**When you use it:** You work with Element objects when you want to query or modify the object as a whole—for example, getting a wall's type, changing its parameters, or deleting it from the project.

**Example:**

```csharp
// Get an Element by its ID
ElementId wallId = new ElementId(12345);
Wall wallElement = doc.GetElement(wallId) as Wall;

// Access a property of the whole element
double wallLength = wallElement.get_Parameter(BuiltInParameter.CURVE_ELEM_LENGTH).AsDouble();
```

---

## I

### ISelectionFilter Interface

The `ISelectionFilter` interface allows you to control what elements a user can pick during a selection operation. The interface has two methods you must implement.

**`AllowElement(Element elem)`**

* **Purpose**
    * Called for each element that is candidate for selection.
* **Params**
    * `Element elem` - the element being checked. 
* **Returns**
    * `true` - the element is selectable.
    * `false` - the element is not selectable.

For example, if you only want the user to be able to select walls:

```csharp
public bool AllowElement(Element elem)
{
    if (elem.Category.Id.IntegerValue == (int)BuiltInCategory.OST_Walls)
    {
        return true;
    }
    return false;
}
```

All categories within the `BuiltInCategory` enumeration can be [found here.](https://www.revitapidocs.com/2019/ba1c5b30-242f-5fdc-8ea9-ec3b61e6e722.htm)

**`AllowReference(Reference reference, XYZ position)`**

* **Purpose**
    * This method is used to filter sub-elements, which are parts of an element that can be selected individually, such as faces, edges, or curves.
* **Params**
    * `Reference reference` - if `AllowElement` returns `true`, Revit then identifies the specific sub-element that the user is pointing at.
    * `XYZ position` - Likewise, this returns where the user clicked to make the selection allowing a sub-element selection.
* **Returns**
    * `true` - The referenced sub-element is selectable.
    * `false` - The referenced sub-element is not selectable.

---

## R

### Reference

A Reference is a more granular and specific pointer. It doesn't just point to an element; it can point to a specific geometric part of an element, such as a face, an edge, or a curve. It acts as a stable and reliable link to a piece of geometry.

**What it Represents:** A precise geometric entity.

* **An entire element.**
* **A sub-element**, like a specific face of a wall or an edge of a beam.
* The geometry of an element within a linked Revit model.

**Key Properties:**

* **ElementId:** Tells you which Element the reference belongs to.
* **ElementReferenceType:** Specifies what kind of geometry is being referenced (e.g., FACE, EDGE, CURVE).
* **ConvertToStableRepresentation():** A crucial method that generates a unique string. This string can be saved and used to find the exact same geometric reference even after the project has been closed and reopened.

**Analogy:** Think of a Reference as the specific car door handle, the windshield, or the entire car itself. It's a more precise address.

**When you use it:** You almost always encounter Reference objects during user selections. When a user clicks on an object in the Revit UI, the API gives you a Reference because it needs to tell you exactly what was clicked—not just the element, but the specific face or edge. It's also essential for creating dimensions and constraints, which need to be attached to specific geometry.

**Example:**

```csharp
// Prompt the user to pick something
Reference pickedRef = uidoc.Selection.PickObject(ObjectType.Face, "Select a face");

// Get the Element that owns the reference
Element elem = doc.GetElement(pickedRef.ElementId);

// Get the specific geometry (the Face) from the reference
Face selectedFace = elem.GetGeometryObjectFromReference(pickedRef) as Face;
```

---

## T

### Transactions


*Transactions are objects that capture the changes to the Revit model. Changes to the Revit model can only be performed when there is an active transaction to do so. Transactions can either be committed – which means that their changes are written/recorded into the model – or rolled-back – which means their changes are undone.* [^1]


At the beginning of your code, you need to use a **Transaction Attribute.** This set to *manual* if you want to interactive with the model and *readonly* if you only want to read from the model. For example:

```csharp
[Transaction(TransactionMode.Manual)]
```

By setting the TransactionMode to Manual, the developer is explicitly stating that they will be responsible for managing transactions within the code. This means the script must:

* ***Instantiate a Transaction object:*** ```Transaction trans = new Transaction(doc);```
* ***Start the transaction:*** Using the ```trans.Start("Descriptive Name");``` method before making any changes to the model. The string argument provides a name for the transaction that will appear in Revit's undo history.
* ***Commit or roll back the transaction:*** After the modifications are complete, ```trans.Commit();``` saves the changes to the model. If an error occurs or the changes need to be discarded, ```trans.RollBack();``` would be used instead.

---

### Footnotes

[^1]: [Autodesk Revit Guides](https://www.autodesk.com/support/technical/article/caas/tsarticles/ts/zPJm3n5B62MM5YIkHLHgk.html)