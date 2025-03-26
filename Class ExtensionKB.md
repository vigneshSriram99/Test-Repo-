D365FO extenstion Class XML Creation Guidelines

🎯 Objective
This guide provides a comprehensive set of instructions to generate XML-based extenstion class definitions for Dynamics 365 Finance & Operations (D365FO)
### Guidelines:                       
1. **Class Structure**:
- The class must be enclosed within `<AxClass>` tags.
- Define the class name using `<Name>`.
- Use `<SourceCode>` to encapsulate the class declaration and methods.

2. **Class Declaration**:
- The class must start with the `public class` definition.
- Declare necessary variables at the beginning within a `<![CDATA[]]>` block inside `<Declaration>`.

3. **Methods**:
- Methods should be enclosed within `<Methods>`.
- Each method must have a `<Name>` tag specifying its name.
- If the TDD provides only a description or pseudo-code, write the actual business logic in X++ based on standard D365FO technical development practices.
- The method source code should be inside `<Source>` wrapped in a `<![CDATA[]]>` block.
- Include relevant parameters, return types, and proper error handling.

4. **Best Practices**:
- Ensure proper encapsulation and avoid unnecessary public variables.
- Follow standard naming conventions used in D365FO.
- Maintain readability by structuring XML elements properly.
- Use meaningful parameter names and provide necessary inline comments in the X++ code.

5. **Class Naming Standard:**
- Prefix with `AVA` followed by `<ObjectName>` and `<TypeAbbreviation>_Extension`.
- Abbreviations:
 - Table = `Dbt` (e.g., AVASalesTableDbt_Extension)
 - Class = `Cls` (e.g., AVASalesTableCls_Extension)
 - Form = `Frm` (e.g., AVASalesTableFrm_Extension)
 - Form Data Source = `DS` (e.g., AVASalesTableFrm_SalesTableDS_Extension)
 - Form Data Field = `Fld` (e.g., AVASalesTableFrm_SalesTableDS_SalesIdFld_Extension)
 - Form Control = `Ctrl` (e.g., AVASalesTableFrm_MCRCustomerServiceCtrl_Extension)

6. **Basic Structure:**
- Use `[ExtensionOf(objectStr(<ObjectType>))]` with the correct object type:
 - `classStr` for Classes
 - `tableStr` for Tables
 - `formStr` for Forms
 - `formDataSourceStr` for Form Data Sources
 - `formDataFieldStr` for Form Data Fields
 - `formControlStr` for Form Controls
- Define the class as `final` with the naming convention followed by `_Extension`.
- Implement the method using `next.<MethodName>()` before or after custom logic.           

### **Output Rules**:
- **Provide only the XML output** without additional explanations.
- **Do not include** `xml``` ``` ` in the output.
- **Write actual business logic** in the methods where needed instead of placeholders.
- **Ensure there’s no a blank line before <?xml version=""1.0"" encoding=""utf-8""?>                               
Your response must strictly follow these rules and generate correct XML-based class definitions for D365FO.
You must provide only the xml without any explanantion in the output also donot include xml``` ```` in the output xml
### Example:
###  Class Extension:
        Input:
        - Object Type: Class
        - Class Name: SalesTableType
        - Method: validateWrite,validateSalesLineDelete

        Output:
        <?xml version=""1.0"" encoding=""utf-8""?>
        <AxClass xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"">
          <Name>AVASalesTableTypeCls_Extension</Name>
          <SourceCode>
            <Declaration><![CDATA[
        [ExtensionOf(classStr(SalesTableType))]
        internal final class AVASalesTableTypeCls_Extension
        {
        }
        ]]></Declaration>
            <Methods>
              <Method>
                <Name>validateWrite</Name>
                <Source><![CDATA[
            boolean validateWrite()
            {
                boolean ret = next validateWrite();

                // Custom validation logic
                if (this.SalesStatus == SalesStatus::Delivered)
                {
                    throw error(""Sales order already delivered"");
                }
                return ret;
            }

        ]]></Source>
              </Method>
              <Method>
                <Name>validateSalesLineDelete</Name>
                <Source><![CDATA[
            protected boolean validateSalesLineDelete(SalesLine _salesLine, boolean _mcrCancel)
            {
                next validateSalesLineDelete(_salesLine, _mcrCancel);
                return _salesLine.validateDelete(true, _mcrCancel);
            }

        ]]></Source>
              </Method>
            </Methods>
          </SourceCode>
        </AxClass>
        ---
   ###  Table Extension:
        Input:
        - Object Type: Table
        - Table Name: SalesTable
        - Method: insert

        Output:
        [ExtensionOf(tableStr(SalesTable))]
        <?xml version=""1.0"" encoding=""utf-8""?>
        <AxClass xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"">
          <Name>AVASalesTableDbt_Extension</Name>
          <SourceCode>
            <Declaration><![CDATA[
        [ExtensionOf(tableStr(SalesTable))]
        internal final class AVASalesTableDbt_Extension
        {
        }
        ]]></Declaration>
            <Methods>
              <Method>
                <Name>insert</Name>
                <Source><![CDATA[
           void insert(boolean _skipMarkup)
            {
                next insert();

                // Custom logic after insert
                this.MyCustomField = ""Custom value"";
            }

        ]]></Source>
              </Method>
            </Methods>
          </SourceCode>
        </AxClass>

        ---
   ### Form Extension:
        Input:
        - Object Type: Form
        - Form Name: SalesTable
        - Method: init
        Output:
       <?xml version=""1.0"" encoding=""utf-8""?>
        <AxClass xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"">
          <Name>AVASalesTableFrm_Extension</Name>
          <SourceCode>
            <Declaration><![CDATA[
        [ExtensionOf(formStr(SalesTable))]
        internal final class AVASalesTableFrm_Extension
        {
        }
        ]]></Declaration>
            <Methods>
              <Method>
                <Name>init</Name>
                <Source><![CDATA[
            public void init()
            {
                next init();

                // Custom logic after init
                this.enableButton();
            }

        ]]></Source>
              </Method>
            </Methods>
          </SourceCode>
        </AxClass>
        ---  
   ### Form Data Source Extension:
        Input:
        - Object Type: FormDataSource
        - Form Name: SalesTable
        - Data Source: SalesTable
        - Method: init

        Output:
        <?xml version=""1.0"" encoding=""utf-8""?>
        <AxClass xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"">
          <Name>AVASalesTableFrm_SalesTableDS_Extension</Name>
          <SourceCode>
            <Declaration><![CDATA[
        [ExtensionOf(formDataSourceStr(SalesTable, SalesTable))]
        internal final class AVASalesTableFrm_SalesTableDS_Extension
        {
        }
        ]]></Declaration>
            <Methods>
              <Method>
                <Name>init</Name>
                <Source><![CDATA[
            public void init()
            {
                next init();

                // Custom logic after data source init
                this.query().addRange(fieldNum(SalesTable, SalesId)).value(""SO-1234"");
            }

        ]]></Source>
              </Method>
            </Methods>
          </SourceCode>
        </AxClass>
        ---         
### Form Data Field Extension:
    Input:
    - Object Type: FormDataField
    - Form Name: SalesTable
    - Data Source: SalesTable
    - Field Name: SalesId
    - Method: modified
    Output:
    <?xml version=""1.0"" encoding=""utf-8""?>
    <AxClass xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"">
      <Name>AVASalesTableFrm_SalesTableDS_SalesIdFld_Extension</Name>
      <SourceCode>
        <Declaration><![CDATA[
    [ExtensionOf(formDataFieldStr(SalesTable,SalesTable, SalesId))]
    internal final class AVASalesTableFrm_SalesTableDS_SalesIdFld_Extension
    {
    }
    ]]></Declaration>
        <Methods>
          <Method>
            <Name>modified</Name>
            <Source><![CDATA[
        public void modified()
        {
            next modified();

            // Custom logic after modification
            if (SalesTable.SalesId == ""SO-9999"")
            {
                info(""Special Order detected!"");
            }
        }

    ]]></Source>
          </Method>
        </Methods>
      </SourceCode>
    </AxClass>

    ---
### Form Control Extension:
    Input:
    - Object Type: FormControl
    - Form Name: SalesTable
    - Control Name: MCRCustomerService
    - Method: clicked

    Output:
    <?xml version=""1.0"" encoding=""utf-8""?>
    <AxClass xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"">
      <Name>AVASalesTableFrm_MCRCustomerServiceCtrl_Extension</Name>
      <SourceCode>
        <Declaration><![CDATA[
    [ExtensionOf(formControlStr(SalesTable, MCRCustomerService))]
    internal final class AVASalesTableFrm_MCRCustomerServiceCtrl_Extension
    {
    }
    ]]></Declaration>
        <Methods>
          <Method>
            <Name>clicked</Name>
            <Source><![CDATA[
        public void clicked()
        {
            next clicked();

            // Custom logic after button click
            info(""Customer Service button clicked!"");
        }

    ]]></Source>
          </Method>
        </Methods>
      </SourceCode>
    </AxClass>
