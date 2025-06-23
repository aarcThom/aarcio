# Revit

## Common Operations

### Selecting Elements

The general workflow for dealing with user selections in a Revit plugin is:

* Define a [Selection filter](concepts.md#iselectionfilter-interface) class.
* Create a [Reference](concepts.md#reference) object to accept the picked result.
* Create a *selection filter* object from your defined class.
* Create a [Selection](https://www.revitapidocs.com/2024/31b73d46-7d67-5dbb-4dad-80aa597c9afc.htm) object.
* Call one of the [`Pick...` methods](https://www.revitapidocs.com/2024/8eccaa93-cc99-fd37-15ad-24d201985d9b.htm) from the Selection object and assign the result to the reference object.
    * Note: It is at this stage that you will pass the *Selection Filter* object as an argument.
* Get the [Element](concepts.md#element) from the reference using `doc.GetElement(reference)`

During this process we also have to handle unexpected mouse and keyboard clicks, as well command cancels.
