# Table Extension and Table Extension method or COC in Dynamics 365 Finance & Operations (D365FO)

## Overview of Table Extensions

When creating a table extension in D365FO, the functionality provided is limited. 
You cannot modify the table structure directly except in specific cases.  you must follow a specific sequence.

i.e 
1.FieldGroupExtensions
2.FieldGroups 
3.FieldModifications
4.Fields
5.FullTextIndexes
6.Indexes
7.Mappings
8.PropertyModifications
9.RelationExtensions
10.RelationModifications
11.Relations

Below are the key points about table extensions:

- **Naming Convention**: When extending a table, follow the naming convention:
  - Example: If extending the `CustGroup` table, the extension name would be `CustGroup.AVAExtension`.

- **FieldGroupExtensions**

You can add fields to these existing field groups

- **Field Groups**

Field groups allow you to organize and manage fields in a logical manner.

Additional field groups (e.g., **Test**) can be specified by the user or in the TDD. and we can add fields in the field group.
  

- **Field Modifications**: 
   You can add new fields and modify properties of existing fields. The modifications you can make to existing fields are limited to a few attributes.
  

## existing Table Fields Properties modifications

### Common Field Properties:

You can modify several properties when working with table fields in a table extension:

| Property Name           | Description                                           | Possible Values            | Default Value |
|-------------------------|-------------------------------------------------------|----------------------------|---------------|
| **HelpText**             | Provides a tooltip or description for the field.     | Any text                   | Empty         |
| **Label**                | Specifies the display name of the field in the UI.    | Any text                   |empty         |
| **Visible**              | Controls whether the field is visible in forms.      | Yes / No                   | Yes           |
| **ExtendedDataTypes**    | Links the field to an Extended Data Type (EDT).       | EDT name                   | Empty         |


add new field with existing table 

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
9. **Mandatory:** Specifies whether the field is required.The values are No and Yes
10. **EnumValue:** *(For AxTableFieldEnum only)* Default enum value.
11. **StringSize:** *(For AxTableFieldString only)* Maximum string length.Default string value 10
12.If the TDD specifies ""no"" or ""yes"" for thr table propert like allowedit,Mandatory,alloweditoncreate,AllowEditOnCreate then we need to chnage into ""No"" or ""Yes"" values in XML.
---


## Indexes

Indexes are used to improve data retrieval efficiency. In table extensions, you can only add new indexes and its fields.

### Index Properties:
- **Name**: Index name.
- **AllowDuplicates**: Whether duplicate values are allowed. *(Default: Yes)*
- **AlternateKey**: Whether the index is an alternate key. *(Default: No)*
- **ConfigurationKey**: Controls the availability of the index based on the configuration key.

---

- **Table Properties**: 
  - You can only modify certain properties like **Title Field 1** and **Title Field 2**.
  - Other properties of the table cannot be modified directly in the table extension.

## Relations

Relations help maintain data integrity and ensure metadata accuracy.

### Types of Relations:

1. **AxTableRelation**:
   - **Name**: Relation name.
   - **Cardinality**: Relationship type (e.g., One-to-One, One-to-Many).
   - **OnDelete**: Delete behavior (e.g., Cascade).
   - **RelatedTable**: Referenced table.
   - **RelatedTableCardinality**: Cardinality of the related table.
   - **RelatedTableRole**: Role of the related table.
   - **RelationshipType**: Relationship type (e.g., Composition, Association).

2. **ForeignKeyRelation**:
   - Similar to **AxTableRelation**, with additional properties for enforcing foreign key constraints.

---

## Limitations in Table Extensions

There are specific limitations when working with table extensions:

1. **Delete Actions**: You cannot add new delete actions to the table in the extension.
2. **Methods**: You cannot add or modify methods directly within a table extension. However, you can use **Chain of Command (CoC)** to add or modify methods for the extended table.

---

added 2 example xml output for the reference

For Table extension example :

Table Extension Requirement Document

1. Table Name:
   - Name: CustGroup.AVATrainig
   - Description: Extension of the CustGroup table with custom fields, field modifications, and relations.

2. Field Group Extensions:
   - Field Group: AutoReport
     - Add FieldReal1 to the AutoReport field group.

3. Field Modifications:
   - Modify the following properties for the CustGroup table:
     - HelpText: sdfsdgsdgdsfg
     - Label: sdgsdgdfgsd

4. New Field Addition:
   - Field Name: FieldReal1
   - Field Type: AxTableFieldReal
   - Extended Data Type (EDT): BOMQty
   - HelpText: sfsdfsdf
   - Label: sdfsdgdfg
   - Ignore EDT Relation: Yes

5. Indexes:
   - Index Name: Index1
     - Include FieldReal1 in this index.

6. Table Relations:
   - Relation Name: Relation1
     - OnDelete: Cascade
     - Related Table: BankLCCustTrans
     - Constraints:
       - CustAccountNumSeq in CustGroup relates to BankLCExportLine in BankLCCustTrans.
       
Detailed Field Information:
1. New Field: FieldReal1
   - Field Type: AxTableFieldReal
   - Extended Data Type (EDT): BOMQty
   - HelpText: sfsdfsdf
   - Label: sdfsdgdfg
   - Ignore EDT Relation: Yes
   - Indexed: Included in Index1

2. Existing Field Modifications:
   - Modify HelpText: sdfsdgsdgdsfg
   - Modify Label: sdgsdgdfgsd

3. Field Group Modifications:
   - Add FieldReal1 to AutoReport field group.

4. Indexes:
   - Create Index1 for FieldReal1.

5. Table Relations:
   - Create Relation1 with Cascade delete behavior between CustGroup and BankLCCustTrans.

Summary:
- Add new field: FieldReal1 (AxTableFieldReal, BOMQty).
- Modify existing fields' HelpText and Label for CustGroup.
- Add FieldReal1 to AutoReport field group.
- Create Index1 with FieldReal1.
- Establish Relation1 with BankLCCustTrans table, enforcing Cascade delete.

Methods (New and Existing):

   **A. New Method (via Chain of Command - COC) Example:**
   - **Method Name:** `calculateDiscountAmount`
   - **Description:** This new method calculates the discount amount for a CustGroup based on the contract and discount percentage.


Existing Method Modification Example:

Method Name: validateCustAccount
Description: This existing method validates the customer account in the CustGroup.
Modification Requirement:
Extend the method to add additional validation for a custom business rule (e.g., checking a custom flag or condition).


output : For Table Extension.

<?xml version="1.0" encoding="utf-8"?>
<AxTableExtension xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<Name>CustGroup.AVAExtenstion</Name>
	<FieldGroupExtensions>
		<AxTableFieldGroupExtension>
			<Name>AutoReport</Name>
			<Fields>
				<AxTableFieldGroupField>
					<DataField>FieldReal1</DataField>
				</AxTableFieldGroupField>
			</Fields>
		</AxTableFieldGroupExtension>
	</FieldGroupExtensions>
	<FieldGroups />
	<FieldModifications>
		<AxExtensionModification>
			<Name>CustGroup</Name>
			<PropertyModifications>
				<AxPropertyModification>
					<Name>HelpText</Name>
					<Value>sdfsdgsdgdsfg</Value>
				</AxPropertyModification>
				<AxPropertyModification>
					<Name>Label</Name>
					<Value>sdgsdgdfgsd</Value>
				</AxPropertyModification>
			</PropertyModifications>
		</AxExtensionModification>
	</FieldModifications>
	<Fields>
		<AxTableField xmlns=""
			i:type="AxTableFieldReal">
			<Name>FieldReal1</Name>
			<ExtendedDataType>BOMQty</ExtendedDataType>
			<HelpText>sfsdfsdf</HelpText>
			<IgnoreEDTRelation>Yes</IgnoreEDTRelation>
			<Label>sdfsdgdfg</Label>
		</AxTableField>
	</Fields>
	<FullTextIndexes />
	<Indexes>
		<AxTableIndex>
			<Name>Index1</Name>
			<Fields>
				<AxTableIndexField>
					<DataField>FieldReal1</DataField>
				</AxTableIndexField>
			</Fields>
		</AxTableIndex>
	</Indexes>
	<Mappings />
	<PropertyModifications />
	<RelationExtensions />
	<RelationModifications />
	<Relations>
		<AxTableRelation>
			<Name>Relation1</Name>
			<OnDelete>Cascade</OnDelete>
			<RelatedTable>BankLCCustTrans</RelatedTable>
			<Constraints>
				<AxTableRelationConstraint xmlns=""
					i:type="AxTableRelationConstraintField">
					<Name>CustAccountNumSeq</Name>
					<Field>CustAccountNumSeq</Field>
					<RelatedField>BankLCExportLine</RelatedField>
				</AxTableRelationConstraint>
			</Constraints>
		</AxTableRelation>
	</Relations>
</AxTableExtension>


**For table Methods i.e COC:**

<?xml version="1.0" encoding="utf-8"?>
<AxClass xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<Name>AVACustGroupDbt_Extension</Name>
	<SourceCode>
		<Declaration><![CDATA[
[ExtensionOf(tableStr(CustGroup))]
internal final class AVACustGroupDbt_Extension
{
}
]]></Declaration>
		<Methods>
			<Method>
				<Name>validateCustAccount</Name>
				<Source><![CDATA[
    public boolean validateCustAccount()
    {
        boolean result;

        // Call the original validateCustAccount method
        result = next validateCustAccount();

        // Add custom validation logic
        if (this.CustomFlag == true)
        {
            result = false; // Fail validation based on custom rule
        }

        return result;
    }

]]></Source>
			</Method>
			<Method>
				<Name>calculateDiscountAmount</Name>
				<Source><![CDATA[
    public boolean calculateDiscountAmount()
    {
        //Add Your Business Logic for calculating Discount Amount
    }

]]></Source>
			</Method>
		</Methods>
	</SourceCode>
</AxClass>


Example 2 output 


<?xml version="1.0" encoding="utf-8"?>
<AxTableExtension xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<Name>DMFDefinitionGroup.AVAExtenstion</Name>
	<FieldGroupExtensions />
	<FieldGroups />
	<FieldModifications />
	<Fields>
		<AxTableField xmlns=""
			i:type="AxTableFieldEnum">
			<Name>AVACC_Import</Name>
			<ExtendedDataType>NoYesId</ExtendedDataType>			
			<EnumType>NoYes</EnumType>
		</AxTableField>
	</Fields>
	<FullTextIndexes />
	<Indexes />
	<Mappings />
	<PropertyModifications />
	<RelationExtensions />
	<RelationModifications />
	<Relations />
</AxTableExtension>

