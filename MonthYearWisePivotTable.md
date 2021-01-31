1. Table and Data
```
CREATE TABLE [dbo].[attendance](
	[attMonth] [tinyint] NOT NULL,
	[attYear] [int] NOT NULL,
	[employeeId] [int] NOT NULL,
	[days] [tinyint] NULL,
 CONSTRAINT [PK_attendance] PRIMARY KEY CLUSTERED 
(
	[attMonth] ASC,
	[attYear] ASC,
	[employeeId] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (1, 2021, 19, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (1, 2021, 20, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (1, 2021, 21, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (1, 2021, 22, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (1, 2021, 23, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (1, 2021, 24, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 15, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 19, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 20, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 21, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 22, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 25, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 28, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 31, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 34, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 35, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 39, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 40, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 42, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 43, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 44, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 45, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 46, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 47, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 48, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 49, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 50, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 51, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 52, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 53, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 54, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 55, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 56, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 72, 31)
GO
INSERT [dbo].[attendance] ([attMonth], [attYear], [employeeId], [days]) VALUES (12, 2020, 73, 31)
GO

```
2. Apply PIVOT on table
```
-- =============================================
-- Author:		<Author,,Name>
-- Create date: <Create Date,,>
-- Description:	<Description,,>
-- ============================================= EXEC GetAttendanceDetail '2020-11-01', '2021-01-01'
ALTER PROCEDURE GetAttendanceDetail
	@StartDate  DATETIME, @EndDate DATETIME
AS
BEGIN
	--DECLARE @StartDate  DATETIME = '2020-11-01', @EndDate    DATETIME = '2021-01-01'
	declare @qry nvarchar(500);
	SET @qry=N'SELECT * FROM (SELECT employeeId, [days], CONCAT(attMonth,''-'',attYear)MY FROM attendance) StudentResults'
	+' PIVOT (SUM([days]) FOR [MY] IN ('+
		(SELECT STUFF((
				SELECT  ',[' + CONVERT(NVARCHAR(55),A.[month])+'-'+CONVERT(NVARCHAR(55),A.[year])+']'
				--FROM getMonthsDtl(@StartDate, @EndDate)
				FROM (
					SELECT YEAR(y.dt) AS Year,
					MONTH(y.dt) AS Month,
					DATENAME(MONTH, y.dt) AS MonthName
					FROM    master.dbo.spt_values x
					CROSS APPLY (SELECT DATEADD(MONTH, x.number, @StartDate)) AS y(dt)
					WHERE   x.type = 'P'        
					AND     x.number <= DATEDIFF(MONTH, @StartDate, @EndDate)
				)A
				FOR XML PATH('')), 1, 1, ''))
	+')) AS PivotTable';
	EXEC sp_executesql @qry
END
GO
```
