D365FO Class XML Creation Guidelines
## 🎯 **Objective**
This guide provides a comprehensive set of **instructions** to generate **XML-based class definitions** for Dynamics 365 Finance & Operations (D365FO)
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

 ### **Output Rules**:
 - **Provide only the XML output** without additional explanations.
 - **Do not include** `xml``` ``` ` in the output.
 - **Write actual business logic** in the methods where needed instead of placeholders.
 - **Ensure there’s no a blank line before <?xml version=""1.0"" encoding=""utf-8""?>
 Your response must strictly follow these rules and generate correct XML-based class definitions for D365FO.
 You must provide only the xml without any explanantion in the output also donot include xml``` ```` in the output xml

### Example:
<?xml version=""1.0"" encoding=""utf-8""?>
<AxClass xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"">
    <Name>AVAMFGReRelease</Name>
    <SourceCode>
        <Declaration><![CDATA[
/// <summary>
/// Class to execute ProductonOrderstatus ReRelease
/// </summary>
public class AVAMFGReRelease
{
}
]]></Declaration>
        <Methods>
            <Method>
                <Name>run</Name>
                <Source><![CDATA[
    /// <summary>
    /// Executes business logic for production order rerelease
    /// </summary>
    /// <param name = ""_prodTable"">ProdTable</param>
    public void run(ProdTable _prodTable)
    {
        this.createAVAMFGRouteOperationsTable(_prodTable);
    }

]]></Source>
            </Method>
            <Method>
                <Name>createAVAMFGRouteOperationsTable</Name>
                <Source><![CDATA[
    /// <summary>
    /// create route operation table
    /// </summary>
    /// <param name = ""_prodTable"">ProdTable</param>
    public void createAVAMFGRouteOperationsTable(ProdTable _prodTable)
    {
        ProdRoute                   prodRoute;
        AVAMFGRouteOperationsTable  routeOperations;
        ProdTable                   prodTableLoc;

        ttsbegin; 
        delete_from routeOperations
            where routeOperations.User == curUserId()
            && routeOperations.ProdId == _prodTable.ProdId;
        ttscommit;

        while select prodTableLoc where prodTableLoc.ProdId == _prodTable.ProdId
            join prodRoute where prodRoute.ProdId == prodTableLoc.ProdId
        {
            routeOperations.ProdId                  =       prodRoute.ProdId;                                          
            routeOperations.insert();
            this.createAVAMFGbomItemLine(_prodTable, routeOperations.OprNum, routeOperations.RecId);
        }
    }

]]></Source>
            </Method>
            <Method>
                <Name>createAVAMFGbomItemLine</Name>
                <Source><![CDATA[
    /// <summary>
    /// Creates bom bomItemLine
    /// </summary>
    /// <param name = ""_prodTable"">ProdTable</param>
    /// <param name = ""_OprNum"">OprnNum</param>
    /// <param name = ""_recId"">_recId</param>
    public void createAVAMFGbomItemLine(ProdTable _prodTable,int _OprNum, int64 _recId)
    {
        ProdBOM                    prodBom;
        ProdTable                  prodTableLoc;
        AVAMFGBomItemLine          bomItemLine;
        RecordInsertList           bomItemLineList;
        InventDim                  inventDim;
        RecId                      recIdRelation = _recId;

        bomItemLineList = new RecordInsertList(tableNum(AVAMFGBomItemLine));

        while select prodTableLoc
            where prodTableLoc.ProdId == _prodTable.ProdId
              join  prodBom where prodBom.ProdId == prodTableLoc.ProdId
              join  inventDim where prodBom.InventDimId == inventDim.inventDimId
        {
            if (prodBom.OprNum == _OprNum )
            {
                bomItemLine.ProdId                      =       prodTableLoc.ProdId;                                              
                bomItemLineList.add(bomItemLine);
            }
        }
        ttsbegin;
        bomItemLineList.insertDatabase();
        ttscommit;
    }

]]></Source>
            </Method>
            <Method>
                <Name>main</Name>
                <Source><![CDATA[
    /// <summary>
    /// Main method to trigger the execute the business logic
    /// </summary>
    /// <param name = ""_args"">args</param>
    public static void main(Args   _args)
    {
        Args                       args;
        AVAMFGReRelease            reReleaseClass;
        FormRun                    formRun;
        ProdTable                  prodTable;

        reReleaseClass = new AVAMFGReRelease();
        args           = new Args();

        if( _args.record() && _args.record().TableId == TableNum(ProdTable))
        {
            prodTable =  _args.record();
        }

        ttsbegin;
        reReleaseClass.run(prodTable);
        ttscommit;

        args.record(prodTable);
        args.name(formStr(AVAMFGReRelease));
        formRun = classFactory.formRunClass(args);
        formRun.init();
        formrun.run();
        formrun.wait();
    }

]]></Source>
            </Method>
        </Methods>
    </SourceCode>
</AxClass>
