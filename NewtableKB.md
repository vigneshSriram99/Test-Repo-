# Creating a Table in D365FO 

When creating a new table in D365FO via XML, you must follow a specific sequence. The XML should include tags for each part of the table—even if only a few properties differ from their default values. If a property isn’t provided by the TDD (Table Definition Document) or user input, or if its value matches the default, it need not be included in the generated XML.
## XML Structure Sequence

The XML should follow this order:

1. **Source Code (Methods)**
   - Includes the class declaration and all static methods.
2. **Table Properties**
   - Examples: table name, label, form reference, configuration key, etc.
3. **Delete Actions**
   - Define how deletions are handled (e.g., cascade, restricted).
4. **Field Groups**
   - Include both default groups (e.g., AutoReport, AutoLookup) and any additional groups.
5. **Fields**
   - Define all table fields with their respective properties.
6. **FullTextIndexes**
   - (If applicable)
7. **Indexes**
   - Define primary and any additional indexes.
8. **Mappings**
   - (If applicable)
9. **Relations**
   - Define relationships with other tables, including foreign key and association relations.
10. **State Machines**
    - (If applicable)

---

## Table Properties

Some key table properties with their default values include:

- **ConfigurationKey:** Controls table access. *(Default: Empty)*
- **CountryRegionCodes:** Country/region restrictions. *(Default: Empty)*
- **FormRef:** Associated form reference. *(Default: Empty)*
- **Label:** Display label. *(Default: Empty)*
- **TableGroup:** Table group classification. *(Default: Miscellaneous)*
- **TitleField1 & TitleField2:** Primary and secondary display fields. *(Default: Empty)*
- **CacheLookup:** Caching behavior. *(Default: None)*
- **ClusteredIndex:** Clustered index. *(Default: SurrogateKey)*
- **CreatedBy, CreatedDateTime, ModifiedBy, ModifiedDateTime:** Audit fields. *(Default: No)*
- **PrimaryIndex:** Primary index. *(Default: SurrogateKey)*
- **SaveDataPerCompany:** Data saving mode. *(Default: Empty)*
- **TableType:** Type of table.Default property value is Regular (Values are: Regular,InMemory,TempDB)*

Only properties provided by the user or those that differ from the defaults need to be included.

---

## Field Types and Common Properties

D365FO supports various field types:

- **AxTableFieldString:** Text data (String).
- **AxTableFieldInt:** Integer values.
- **AxTableFieldReal:** Floating-point numbers.
- **AxTableFieldDate:** Dates.
- **AxTableFieldTime:** Time values.
- **AxTableFieldUtcDateTime:** UTC date & time.
- **AxTableFieldEnum:** Enumerated values.
- **AxTableFieldContainer:** Containers (multiple types).
- **AxTableFieldGuid:** GUIDs.
- **AxTableFieldInt64:** 64-bit integers.

**Common properties for each field include:**

1. **Name:** Field name.
2. **AllowEdit:** Indicates if the field is editable.The values are No and Yes.
3. **AllowEditOnCreate:** Editable during record creation.The values are No and Yes.
4. **ConfigurationKey:** Field-specific configuration key.
5. **CountryRegionCodes:** Applicable country/region codes.
6. **ExtendedDataType:** Links the field to an EDT (Extended Data Type).
7. **HelpText:** Description or help text.
8. **Label:** Display label.
9. **Mandatory:** Specifies whether the field is required.The values are No and Yes.
10. **EnumValue:** *(For AxTableFieldEnum only)* Default enum value.
11. **StringSize:** *(For AxTableFieldString only)* Maximum string length.Default string value 10
12.If the TDD specifies ""no"" or ""yes"" for thr table propert like allowedit,Mandatory,alloweditoncreate,AllowEditOnCreate then we need to chnage into ""No"" or ""Yes"" values in XML.

---

## Delete Actions

Delete actions define the behavior when a record is deleted. Key properties include:

- **Name:** Delete action name.
- **Action:** Deletion behavior:
  - **None:** No action.
  - **Cascade:** Delete related records.
  - **Restricted:** Prevent deletion if related records exist.
  - **Cascade+Restricted:** Combination of both.
- **Relation:** Specifies the relationship for the delete action.
- **TableReference:** The referenced table.

---

## Field Groups

Field groups organize fields. Default groups include:

- **AutoReport**
- **AutoLookup**
- **AutoIdentification**
- **AutoSummary**
- **AutoBrowse**

Additional field groups (e.g., **Test**) can be specified by the user. Field groups may also list specific fields (for example, AutoBrowse may include the field `FieldString1`).

---

## Indexes

Indexes improve data retrieval. Index properties include:

- **Name:** Index name.
- **AllowDuplicates:** Whether duplicate values are allowed. *(Default: Yes)*
- **AlternateKey:** Whether the index is an alternate key. *(Default: No)*
- **ConfigurationKey:** Configuration key controlling index availability.

---

## Relations

Relations maintain data integrity and metadata accuracy. There are two main types:

1. **AxTableRelation:**
   - **Properties:**
     - **Name:** Relation name.
     - **Cardinality:** Relationship type (e.g., NotSpecified, ZeroOne,ExactlyOne,ZeroMore,OneMore).
     - **OnDelete:** Delete behavior (e.g., Cascade).
     - **RelatedTable:** Referenced table.
     - **RelatedTableCardinality:** Cardinality of the related table.(NotSpecified,ZeroOne,ExactlyOne)
     - **RelatedTableRole:** Role of the related table.
     - **RelationshipType:** Relationship type (e.g.,NotSpecified,Composition, Association).

2. **ForeignKeyRelation:**
   - Similar properties with adjustments specific to foreign key constraints.

---

## Example Requirement: Create a Table Named **Table1**

### Fields

The table should include the following fields:

| Field Name         | Data Type        | Extended Data Type (EDT) | Configuration Key | Mandatory |
|--------------------|------------------|--------------------------|-------------------|-----------|
| **FieldString1**   | String (20)      | –                        | Asset             | Yes       |
| **FieldInt1**      | Integer          | AssetRuleValidIcon       | Asset             | Yes       |
| **FieldReal1**     | Real             | ABCPercentA              | Bank              | Yes       |
| **FieldDate1**     | Date             | ActionDate               | Asset             | Yes       |
| **FieldTime1**     | Time             | FromTime                 | Asset             | Yes       |
| **FieldUtcDateTime1** | UtcDateTime   | CopyDateTime             | Asset             | Yes       |
| **FieldEnum1**     | Enum (NoYes)     | NoYesId                  | Bank              | Yes       |
| **FieldContainer1**| Container        | InfologData              | Asset             | Yes       |

### Indexes

- **Primary Index:** `IDX`
- **Clustered Index:** `IDX`

### Relations

The table has the following relationships:

| Related Table | Related Field | Relation Type  | On Delete Action |
|---------------|---------------|----------------|------------------|
| **CustTable** | AccountNum    | Foreign Key    | Cascade Delete   |
| **VendTable** | VendorId      | Foreign Key    | Restricted       |
| **LedgerTable** | LedgerId    | Association    | None             |

### Methods

The table includes the following static methods:

1. `checkExist(CustAccount _custAccount)` – Checks if a customer account exists.
2. `find(CustAccount _custAccount, boolean _forUpdate)` – Retrieves a customer record.
3. `exist(CustAccount _custAccount)` – Determines if a customer record exists.
4. `validateField()` – Validates field values before saving.
5. `insert()` – Implements custom logic before inserting records.
6. `update()` – Implements custom logic before updating records.
7. `delete()` – Handles cleanup when deleting records.

### Configuration & Access

- **Configuration Key:** `Bank`
- **Country/Region Codes:** `IN`
- **Cache Lookup:** `Found`
- **Row Version Change Tracking:** Enabled

### Delete Actions

- **Delete Action for CustTable:** Cascade Delete

### Field Groups

Include the following field groups:

- **AutoReport**
- **AutoLookup**
- **AutoIdentification**
- **AutoSummary**
- **AutoBrowse:** Includes `FieldString1`
- **Test:** Includes `FieldString1`

### Other Properties

- **Form Reference:** `CustTable`
- **Label:** `Test`
- **Audit Fields:** `CreatedBy`, `CreatedDateTime`, `ModifiedBy`, and `ModifiedDateTime` are enabled.
---
## XML Output
<?xml version="1.0" encoding="utf-8"?>
<AxTable xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<Name>Table1</Name>
	<SourceCode>
		<Declaration><![CDATA[
public class Table1 extends common
{
}
]]></Declaration>
		<Methods>
			<Method>
				<Name>checkExist</Name>
				<Source><![CDATA[
    static boolean checkExist(CustAccount _custAccount)
    {
        boolean ret = true;
        if (_custAccount && !CustTable::exist(_custAccount))
        {
            ret = checkFailed(strFmt(CustTable::txtNotExist(), _custAccount));
        }
        return ret;
    }
]]></Source>
			</Method>
			<Method>
				<Name>find</Name>
				<Source><![CDATA[
    static CustTable find(CustAccount _custAccount, boolean _forUpdate = false)
    {
        CustTable custTable;
        if (_custAccount)
        {
            if (_forUpdate)
            {
                custTable.selectForUpdate(_forUpdate);
            }
            select firstonly custTable
                index hint AccountIdx
                where custTable.AccountNum == _custAccount;
        }
        return custTable;
    }
]]></Source>
			</Method>
			<Method>
				<Name>exist</Name>
				<Source><![CDATA[
    static boolean exist(CustAccount _custAccount)
    {
        return _custAccount && (select firstonly RecId from custTable
                                    index hint AccountIdx
                                    where custTable.AccountNum == _custAccount).RecId != 0;
    }
]]></Source>
			</Method>
		</Methods>
	</SourceCode>
	<ConfigurationKey>Bank</ConfigurationKey>
	<CountryRegionCodes>IN</CountryRegionCodes>
	<FormRef>CustTable</FormRef>
	<Label>Test</Label>
	<SubscriberAccessLevel>
		<Read>Allow</Read>
	</SubscriberAccessLevel>
	<TableGroup>Group</TableGroup>
	<TitleField1>FieldString1</TitleField1>
	<TitleField2>FieldReal1</TitleField2>
	<AllowRowVersionChangeTracking>Yes</AllowRowVersionChangeTracking>
	<CacheLookup>Found</CacheLookup>
	<ClusteredIndex>IDX</ClusteredIndex>
	<CreatedBy>Yes</CreatedBy>
	<CreatedDateTime>Yes</CreatedDateTime>
	<ModifiedBy>Yes</ModifiedBy>
	<ModifiedDateTime>Yes</ModifiedDateTime>
	<PrimaryIndex>IDX</PrimaryIndex>
	<DeleteActions>
		<AxTableDeleteAction>
			<Name>CustTable</Name>
			<DeleteAction>Cascade</DeleteAction>
			<Relation></Relation>
			<Table>CustTable</Table>
			<Tags></Tags>
		</AxTableDeleteAction>
	</DeleteActions>
	<FieldGroups>
		<AxTableFieldGroup>
			<Name>AutoReport</Name>
			<Fields />
		</AxTableFieldGroup>
		<AxTableFieldGroup>
			<Name>AutoLookup</Name>
			<Fields />
		</AxTableFieldGroup>
		<AxTableFieldGroup>
			<Name>AutoIdentification</Name>
			<AutoPopulate>Yes</AutoPopulate>
			<Fields />
		</AxTableFieldGroup>
		<AxTableFieldGroup>
			<Name>AutoSummary</Name>
			<Fields />
		</AxTableFieldGroup>
		<AxTableFieldGroup>
			<Name>AutoBrowse</Name>
			<Fields>
				<AxTableFieldGroupField>
					<DataField>FieldString1</DataField>
				</AxTableFieldGroupField>
			</Fields>
		</AxTableFieldGroup>
		<AxTableFieldGroup>
			<Name>Test</Name>
			<Label>Test</Label>
			<Fields>
				<AxTableFieldGroupField>
					<DataField>FieldString1</DataField>
				</AxTableFieldGroupField>
			</Fields>
		</AxTableFieldGroup>
	</FieldGroups>
	<Fields>
		<AxTableField xmlns="" i:type="AxTableFieldString">
			<Name>FieldString1</Name>
			<AllowEdit>No</AllowEdit>
			<AllowEditOnCreate>No</AllowEditOnCreate>
			<ConfigurationKey>Asset</ConfigurationKey>
			<HelpText>safsdfsdgsdg</HelpText>
			<Label>sgdsgdgdfgdfg</Label>
			<Mandatory>Yes</Mandatory>
			<StringSize>20</StringSize>
		</AxTableField>
		<!-- Additional fields for FieldInt1, FieldReal1, etc., follow the same structure -->
	</Fields>
	<FullTextIndexes />
	<Indexes>
		<AxTableIndex>
			<Name>IDX</Name>
			<ConfigurationKey>Asset</ConfigurationKey>
			<Fields>
				<AxTableIndexField>
					<DataField>FieldString1</DataField>
				</AxTableIndexField>
			</Fields>
		</AxTableIndex>
	</Indexes>
	<Mappings />
	<Relations>
		<AxTableRelation>
			<Name>Relation1</Name>
			<Cardinality>OneMore</Cardinality>
			<OnDelete>Cascade</OnDelete>
			<RelatedTable>CustTable</RelatedTable>
			<RelatedTableCardinality>ZeroOne</RelatedTableCardinality>
			<RelationshipType>Association</RelationshipType>
			<Constraints>
				<AxTableRelationConstraint xmlns="" i:type="AxTableRelationConstraintField">
					<Name>FieldString1</Name>
					<Field>FieldString1</Field>
					<RelatedField>AccountNum</RelatedField>
				</AxTableRelationConstraint>
			</Constraints>
		</AxTableRelation>
		<AxTableRelation xmlns="" i:type="AxTableRelationForeignKey">
			<Name>RelationForeignKey1</Name>
			<Cardinality>ExactlyOne</Cardinality>
			<RelatedTable>VendTable</RelatedTable>
			<RelatedTableCardinality>ZeroOne</RelatedTableCardinality>
			<RelationshipType>Association</RelationshipType>
			<Constraints>
				<AxTableRelationConstraint xmlns="" i:type="AxTableRelationConstraintField">
					<Name>FieldInt641</Name>
					<Field>FieldInt641</Field>
					<RelatedField>RecId</RelatedField>
				</AxTableRelationConstraint>
			</Constraints>
		</AxTableRelation>
	</Relations>
	<StateMachines />
</AxTable>
