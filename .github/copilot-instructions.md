# Project Guidelines for Copilot

## Database Development Standards

This project uses SQL Server 2022 with the following conventions:

### Naming Conventions
- Tables: PascalCase, singular (Customer, OrderDetail)
- Columns: PascalCase (FirstName, OrderDate)
- Stored procedures: usp_ActionEntity (usp_GetCustomerOrders)
- Views: vw_EntityName (vw_ActiveCustomers)
- Indexes: IX_TableName_ColumnName

### T-SQL Style
- Use explicit column lists in SELECT statements (avoid SELECT *)
- Always include schema prefix (dbo.TableName)
- Use ANSI JOIN syntax, not comma-separated tables
- Include error handling in all stored procedures
- Use TRY...CATCH blocks for data modification operations

### Security Requirements
- Never generate GRANT statements to public
- Use parameterized queries, never concatenate user input
- Avoid dynamic SQL when possible

### Performance Guidelines
- Suggest appropriate indexes when creating tables
- Prefer SET NOCOUNT ON in stored procedures
- Use EXISTS instead of COUNT for existence checks

## Error handling pattern

Always use this error handling pattern in stored procedures:

```sql
CREATE PROCEDURE dbo.usp_ExampleProcedure
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        -- Procedure logic here

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        -- Log error details
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();

        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH;
END;