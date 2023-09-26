# Data-Professional-Survey-Power-Bi-Project
Data set is all about earnings of Data Professional as Analyst ,Scientist ,data engineers in different countries.

 Learning- Data loading to Power bi desktop then Transform the data in Power querry Data cleaning in Power Bi like -Removing duplicates ,Adding new custom column for calculation After transform and Data cleaning ,load data into power Bi dashboard view for making dashboard report using different charts, slicer etc.
Task pefrom steps are-

let
    Source = Excel.Workbook(File.Contents("C:\Users\iDEApAD\Downloads\Power BI - Final Project.xlsx"), null, true),
    #"Data Professional Survey_Sheet" = Source{[Item="Data Professional Survey",Kind="Sheet"]}[Data],
    #"Promoted Headers" = Table.PromoteHeaders(#"Data Professional Survey_Sheet", [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Unique ID", type text}, {"Email", type text}, {"Date Taken (America/New_York)", type date}, {"Time Taken (America/New_York)", type time}, {"Browser", type text}, {"OS", type text}, {"City", type text}, {"Country", type text}, {"Referrer", type text}, {"Time Spent", type time}, {"Q1 - Which Title Best Fits your Current Role?", type text}, {"Q2 - Did you switch careers into Data?", type text}, {"Q3 - Current Yearly Salary (in USD)", type text}, {"Q4 - What Industry do you work in?", type text}, {"Q5 - Favorite Programming Language", type text}, {"Q6 - How Happy are you in your Current Position with the following? (Salary)", Int64.Type}, {"Q6 - How Happy are you in your Current Position with the following? (Work/Life Balance)", Int64.Type}, {"Q6 - How Happy are you in your Current Position with the following? (Coworkers)", Int64.Type}, {"Q6 - How Happy are you in your Current Position with the following? (Management)", Int64.Type}, {"Q6 - How Happy are you in your Current Position with the following? (Upward Mobility)", Int64.Type}, {"Q6 - How Happy are you in your Current Position with the following? (Learning New Things)", Int64.Type}, {"Q7 - How difficult was it for you to break into Data?", type text}, {"Q8 - If you were to look for a new job today, what would be the most important thing to you?", type text}, {"Q9 - Male/Female?", type text}, {"Q10 - Current Age", Int64.Type}, {"Q11 - Which Country do you live in?", type text}, {"Q12 - Highest Level of Education", type text}, {"Q13 - Ethnicity", type text}}),
    // "Browser", "OS", "City", "Country", "Referrer"
    #"Removed Columns" = Table.RemoveColumns(#"Changed Type",{"Browser", "OS", "City", "Country", "Referrer"}),
    //  "Q1 - Which Title Best Fits your Current Role?"
    #"Split Column by Delimiter" = Table.SplitColumn(#"Removed Columns", "Q1 - Which Title Best Fits your Current Role?", Splitter.SplitTextByEachDelimiter({"("}, QuoteStyle.Csv, false), {"Q1 - Which Title Best Fits your Current Role?.1", "Q1 - Which Title Best Fits your Current Role?.2"}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Q1 - Which Title Best Fits your Current Role?.1", type text}, {"Q1 - Which Title Best Fits your Current Role?.2", type text}}),
    // "Q1 - Which Title Best Fits your Current Role?.2"
    #"Removed Columns1" = Table.RemoveColumns(#"Changed Type1",{"Q1 - Which Title Best Fits your Current Role?.2"}),
    // "Q5 - Favorite Programming Language"
    #"Split Column by Delimiter1" = Table.SplitColumn(#"Removed Columns1", "Q5 - Favorite Programming Language", Splitter.SplitTextByEachDelimiter({":"}, QuoteStyle.Csv, false), {"Q5 - Favorite Programming Language.1", "Q5 - Favorite Programming Language.2"}),
    #"Changed Type2" = Table.TransformColumnTypes(#"Split Column by Delimiter1",{{"Q5 - Favorite Programming Language.1", type text}, {"Q5 - Favorite Programming Language.2", type text}}),
    // "Q5 - Favorite Programming Language.2"
    #"Removed Columns2" = Table.RemoveColumns(#"Changed Type2",{"Q5 - Favorite Programming Language.2"}),
    // "Q3 - Current Yearly Salary (in USD)"
    #"Duplicated Column" = Table.DuplicateColumn(#"Removed Columns2", "Q3 - Current Yearly Salary (in USD)", "Q3 - Current Yearly Salary (in USD) - Copy"),
    // "Q3 - Current Yearly Salary (in USD) - Copy"
    #"Split Column by Character Transition" = Table.SplitColumn(#"Duplicated Column", "Q3 - Current Yearly Salary (in USD) - Copy", Splitter.SplitTextByCharacterTransition({"0".."9"}, (c) => not List.Contains({"0".."9"}, c)), {"Q3 - Current Yearly Salary (in USD) - Copy.1", "Q3 - Current Yearly Salary (in USD) - Copy.2", "Q3 - Current Yearly Salary (in USD) - Copy.3"}),
    // "Q3 - Current Yearly Salary (in USD) - Copy.3"
    #"Removed Columns3" = Table.RemoveColumns(#"Split Column by Character Transition",{"Q3 - Current Yearly Salary (in USD) - Copy.3"}),
    // "k",""
    #"Replaced Value" = Table.ReplaceValue(#"Removed Columns3","k","",Replacer.ReplaceText,{"Q3 - Current Yearly Salary (in USD) - Copy.2"}),
    // "-",""
    #"Replaced Value1" = Table.ReplaceValue(#"Replaced Value","-","",Replacer.ReplaceText,{"Q3 - Current Yearly Salary (in USD) - Copy.2"}),
    // "+","225"
    #"Replaced Value2" = Table.ReplaceValue(#"Replaced Value1","+","225",Replacer.ReplaceText,{"Q3 - Current Yearly Salary (in USD) - Copy.2"}),
    #"Filtered Rows" = Table.SelectRows(#"Replaced Value2", each true),
    #"Changed Type3" = Table.TransformColumnTypes(#"Filtered Rows",{{"Q3 - Current Yearly Salary (in USD) - Copy.1", Int64.Type}, {"Q3 - Current Yearly Salary (in USD) - Copy.2", Int64.Type}}),
    // ([#"Q3 - Current Yearly Salary (in USD) - Copy.1"]+[#"Q3 - Current Yearly Salary (in USD) - Copy.2"])/2
    #"Added Average Salary" = Table.AddColumn(#"Changed Type3", "Average Salary", each ([#"Q3 - Current Yearly Salary (in USD) - Copy.1"]+[#"Q3 - Current Yearly Salary (in USD) - Copy.2"])/2),
    #"Reordered Columns Average Salary" = Table.ReorderColumns(#"Added Average Salary",{"Unique ID", "Email", "Date Taken (America/New_York)", "Time Taken (America/New_York)", "Time Spent", "Q1 - Which Title Best Fits your Current Role?.1", "Q2 - Did you switch careers into Data?", "Q3 - Current Yearly Salary (in USD)", "Average Salary", "Q4 - What Industry do you work in?", "Q5 - Favorite Programming Language.1", "Q6 - How Happy are you in your Current Position with the following? (Salary)", "Q6 - How Happy are you in your Current Position with the following? (Work/Life Balance)", "Q6 - How Happy are you in your Current Position with the following? (Coworkers)", "Q6 - How Happy are you in your Current Position with the following? (Management)", "Q6 - How Happy are you in your Current Position with the following? (Upward Mobility)", "Q6 - How Happy are you in your Current Position with the following? (Learning New Things)", "Q7 - How difficult was it for you to break into Data?", "Q8 - If you were to look for a new job today, what would be the most important thing to you?", "Q9 - Male/Female?", "Q10 - Current Age", "Q11 - Which Country do you live in?", "Q12 - Highest Level of Education", "Q13 - Ethnicity", "Q3 - Current Yearly Salary (in USD) - Copy.1", "Q3 - Current Yearly Salary (in USD) - Copy.2"}),
    //  "Q11 - Which Country do you live in?"
    #"Split Column by Delimiter2" = Table.SplitColumn(#"Reordered Columns Average Salary", "Q11 - Which Country do you live in?", Splitter.SplitTextByEachDelimiter({"("}, QuoteStyle.Csv, false), {"Q11 - Which Country do you live in?.1", "Q11 - Which Country do you live in?.2"}),
    #"Changed Type4" = Table.TransformColumnTypes(#"Split Column by Delimiter2",{{"Q11 - Which Country do you live in?.1", type text}, {"Q11 - Which Country do you live in?.2", type text}}),
    // "Q11 - Which Country do you live in?.2"
    #"Removed Columns4" = Table.RemoveColumns(#"Changed Type4",{"Q11 - Which Country do you live in?.2"}),
    // "Q4 - What Industry do you work in?"
    #"Split Column by Delimiter3" = Table.SplitColumn(#"Removed Columns4", "Q4 - What Industry do you work in?", Splitter.SplitTextByEachDelimiter({"("}, QuoteStyle.Csv, false), {"Q4 - What Industry do you work in?.1", "Q4 - What Industry do you work in?.2"}),
    #"Changed Type5" = Table.TransformColumnTypes(#"Split Column by Delimiter3",{{"Q4 - What Industry do you work in?.1", type text}, {"Q4 - What Industry do you work in?.2", type text}}),
    // "Q4 - What Industry do you work in?.2"
    #"Removed Columns5" = Table.RemoveColumns(#"Changed Type5",{"Q4 - What Industry do you work in?.2"})
in
    #"Removed Columns5"
