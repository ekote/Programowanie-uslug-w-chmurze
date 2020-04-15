## Tag Limitations
- There are some limitations with using Tags, such as:
- Not all resource types support tags. To determine if you can apply a tag to a resource type, see Tag support for Azure resources. Tag support for Azure resources
- Each resource or resource group can have a maximum of 15 tag name/value pairs. This limitation applies only to tags directly applied to the resource group or resource. A resource group can contain many resources that each have 15 tag name/value pairs. If you have more than 15 values that you need to associate with a resource, use a JSON string for the tag value. The JSON string can contain many values that are applied to a single tag name.
- The tag name is limited to 512 characters, and the tag value is limited to 256 characters. For storage accounts, the tag name is limited to 128 characters, and the tag value is limited to 256 characters.
- Virtual Machines and Virtual Machine Scale Sets are limited to a total of 2048 characters for all tag names and values.
- Tags applied to the resource group are not inherited by the resources in that resource group.
- Tags can't be applied to classic resources such as Cloud Services.
- Tag names can't contain these characters: <, >, %, &, \, ?, /
