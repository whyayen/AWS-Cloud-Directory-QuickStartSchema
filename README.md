# AWS-Cloud-Directory-QuickStartSchema
Note how to use QuickStartSchema on Cloud Directory

## Usage Scenario
In the past, we needed to define Schema first, then published with Major/Minor version, last applied to Directory. But if we needed to change schema frequently, these steps were very complicated. Now, Cloud Directory QuickStartSchema is available. You can apply ManageSchema to your directory. And you wouldn't need to define Schema, Facet, Attributes at first.

Any details, please read [How to rapidly develop applications on Amazon Cloud Directory with Managed Schema
](https://aws.amazon.com/tw/blogs/database/rapidly-develop-applications-on-amazon-cloud-directory-with-managed-schema/)

## Example (AWS Command Line Interface)

### 1. Use `ListManagedSchemaArns` API to list ManagedSchema ARNs.

#### Request
```Shell
aws clouddirectory list-managed-schema-arns 
```

#### Response
```
{
    "SchemaArns": [
        "arn:aws:clouddirectory:::schema/managed/quick_start/1.0/001"
    ]
}
```

If you wanted to know `Facet Name` or `Attributes`, you cloud use [ListFacetNames](https://docs.aws.amazon.com/zh_tw/directoryservice/latest/APIReference/API_ListFacetNames.html), [ListFacetAttributes](https://docs.aws.amazon.com/zh_tw/directoryservice/latest/APIReference/API_ListFacetAttributes.html) API

### 2. Apply `QuickStartSchema` to your directory.

#### Request
```Shell
aws clouddirectory apply-schema --directory-arn <Your Directory Arn Here> \
--published-schema-arn arn:aws:clouddirectory:::schema/managed/quick_start/1.0/001
```

#### Response
```
{
    "AppliedSchemaArn": "arn:aws:clouddirectory:::schema/managed/quick_start/1.0",
    "DirectoryArn": "arn:aws:clouddirectory:{region}:{account id}:directory/{directory id}"
}
```

### 3. Create Object, TypedLink, Policy, etc.

You need to put 2 schema in `SchemaFacets` argument.

  * QuickStartSchema which you applied to your directory.
  * This ARN is provided by Cloud Directory and contains definitions for object types such as Node and Leaf_Node.
  ``arn:aws:clouddirectory:<region>:<accountId>:directory/<directoryId>/schema/CloudDirectory/1.0``
  
#### Request
Please follow instruction format with AWS Offical Document.
https://docs.aws.amazon.com/cli/latest/reference/clouddirectory/create-object.html

```
aws clouddirectory create-object --directory-arn <Your Directory ARN>
--schema-facets
'[
  {
    "SchemaArn": "arn:aws:clouddirectory:::schema/managed/quick_start/1.0",
    "FacetName":"DynamicObjectFacet"
  },
  {
    "SchemaArn":"arn:aws:clouddirectory:{region}:{account id}:directory/{directory id}",
    "FacetName":"NODE"
  }
]'
--object-attribute-list
  'Key={
    SchemaArn=arn:aws:clouddirectory:::schema/managed/quick_start/1.0,
    FacetName=DynamicObjectFacet,
    Name=Test
  },
  Value={
    StringValue=ABCD
  }'
--link-name test
--parent-reference Selector=/
```
