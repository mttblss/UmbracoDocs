---
description: Developer documentation on working with Forms record data.
---

# Working With Record Data

Umbraco Forms includes some helper methods that return records of a given Form, which can be used to output records in your templates using Razor code.

## Available Methods

The methods can be found by injecting the `Umbraco.Forms.Core.Services.IRecordReaderService` interface. For performance reasons, all these methods are paged.

### GetApprovedRecordsFromPage

```csharp
PagedResult<IRecord> GetApprovedRecordsFromPage(int pageId, int pageNumber, int pageSize)
```

Returns all records with the state set to approved from all Forms on the Umbraco page with the id = `pageId` .

### GetApprovedRecordsFromFormOnPage

```csharp
PagedResult<IRecord> GetApprovedRecordsFromFormOnPage(int pageId, Guid.Parse("formId"), int pageNumber, int pageSize)
```

Returns all records with the state set to approved from the Form with the id = `formId` on the Umbraco page with the id = `pageId` as a `PagedResult<IRecord>`.

### GetApprovedRecordsFromForm

```csharp
PagedResult<IRecord> GetApprovedRecordsFromForm(Guid.Parse("formId"), int pageNumber, int pageSize)
```

Returns all records with the state set to approved from the Form with the ID = `formId` as a `PagedResult<IRecord>`.

### GetRecordsFromPage

```csharp
PagedResult<IRecord> GetRecordsFromPage(int pageId, int pageNumber, int pageSize)
```

Returns all records from all Forms on the Umbraco page with the id = `pageId` as a `PagedResult<IRecord>`.

### GetRecordsFromFormOnPage

```csharp
PagedResult<IRecord> GetRecordsFromFormOnPage(int pageId, Guid.Parse("formId"), int pageNumber, int pageSize)
```

Returns all records from the Form with the id = `formId` on the Umbraco page with the id = `pageId` as a `PagedResult<IRecord>`.

### GetRecordsFromForm

```csharp
PagedResult<IRecord> GetRecordsFromForm(Guid.Parse("formId"), int pageNumber, int pageSize)
```

Returns all records from the Form with the ID = formId as a `PagedResult<IRecord>`.

## The returned objects

All of these methods will return an object of the type `PagedResult<IRecord>` so you can iterate through the `IRecord` objects.

The properties available on a `IRecord` are:

```csharp
int Id
FormState State
DateTime Created
DateTime Updated
Guid Form
string IP
int UmbracoPageId
string MemberKey
Guid UniqueId
Dictionary<Guid, RecordField> RecordFields
```

In order to access custom Form fields, these are available in the `RecordFields` property. Furthermore there exists an extension method named `ValueAsString` on `IRecord` in `Umbraco.Forms.Core.Services`, such that you can get the value as string given the alias of the field.

This extension method handles multi-value fields by comma separating the values. E.g. "A, B, C"

## Sample razor script

Sample script that is outputting comments using a Form created with the default comment Form template.

```csharp
@using Umbraco.Core;
@using Umbraco.Cms.Core.Composing;
@using Umbraco.Forms.Core.Services;
@inject IRecordReaderService _recordReaderService;

<ul id="comments">
    @foreach (var record in _recordReaderService.GetApprovedRecordsFromPage(Model.Id, 1, 10).Items)
    {
    <li>
        @record.Created.ToString("dd MMMM yyy")
        @if(string.IsNullOrEmpty(record.ValueAsString("email"))){
            <strong>@record.ValueAsString("name")</strong>
        }
        else{
            <strong>
                <a href="mailto:@record.ValueAsString("email")" target="_blank">@record.ValueAsString("name")</a>
            </strong>
        }
        <span>said</span>
        <p>@record.ValueAsString("comment")</p>
    </li>
    }
</ul>
```
