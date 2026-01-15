• Utilizing regex to extract words
		○ =REGEXEXTRACT(C6, "^(.*) \d+x\d+-\d+$")
		○ Cell value: Incline DB Press 3x8-12
		○ Result: Incline DB Press
	• Utilizing regex to extract rep range and reps
		○ =REGEXEXTRACT(C7, "(\d+x\d+-\d+)$")
		○ Cell value: Incline DB Press 3x8-12
		Result: 3x8-12

• Change text to columns on the last page to the following to retain leading zeros: This works BUT on step 7 you can't just select 'text' and click finish, you have to select each column individually in the preview box and update each column to text, then click finish
• How to automatically adjust starting point for formula based on last row +4:
		○ =Formatting!$A$1 & INDIRECT("'round robin RR'!B" & (ROW()-ROW($A$2))*4 + 2) & Formatting!$B$1 & INDIRECT("'round robin RR'!B" & (ROW()-ROW($A$2))*4 + 3) & Formatting!$B$1 & INDIRECT("'round robin RR'!B" & (ROW()-ROW($A$2))*4 + 4) & Formatting!$B$1 & INDIRECT("'round robin RR'!B" & (ROW()-ROW($A$2))*4 + 5) & Formatting!$E$1
	• Reformatting a date to yyyymmdd format and text:
		○ =TEXT(F2,"yyyymmdd")
	• Formatting tables: highlight cell within table, press alt+o+a
	• Comparing two excel sheets for differences:
		○ =IF(Sheet1!A1 <> Sheet2!A1, "Sheet1:"&Sheet1!A1&" vs Sheet2:"&Sheet2!A1, "")
	• How to return results if a cell contains a specific word or phrase:
		○ =IF(ISNUMBER(SEARCH("id",A25)),A29,"")
	• Ctrl+shift+ + creates new row or column
	• Wildcard search with XLOOKUP
		○ =XLOOKUP("*" & B2 & "*",'RR Routes Postman'!A:A,'RR Routes Postman'!I:I,"",2)
	• Can press F4 to make the cell or array an absolute reference
	• How to return multiple columns for matches via XLOOKUP
		○ If right next to eachother, you can click and drag for the return
		○ =XLOOKUP(A1,$B:$B,CHOOSE({1,2},$C:$C,$D:$D),"")
		○ Can change the index {} above to increase the amount returned by: {1,2,3,4,…,n}
		○ Can also put an index for when there is no match
	• Highlighting duplicate entries:
		○ Sheets: Highlight cells of interest>conditional formatting>custom formula>=COUNTIF($A$2:$A$10,$A2)>1
	• Convert integer to HHMMSS timestamp:
		○ =TEXT(B3/86400, "hhmmss")
	• Auto adjust all cell width and height:
		○ Select all cells of interest
		○ Alt+h+o+I
		○ Alt+h+o+a
	• Select row: shift + space bar
	• Insert row: ctrl + shift + + 
	• To use COUNTIF with multiple criteria (OR condition), you can use the + operator to concatenate multiple COUNTIF functions. Each COUNTIF function will check for a different condition
		○ =COUNTIF(A:A, "criteria1") + COUNTIF(A:A, "criteria2") + COUNTIF(A:A, "criteria3")
	• Extracting text string between two specific things
		○ =IFERROR(MID(E3, SEARCH(":""", E3) + LEN(":"""), SEARCH("""}", E3) - SEARCH(":""", E3) - LEN(":""")), "")
		○ {"customfield-a38db94e-00bb-4a03-b8cc-18102cdd2284":"Outside Upload"}
		○ Result: Outside Upload
	• =MID(A60, SEARCH(""": """, A60) + 4, FIND(""",", A60, SEARCH(""": """, A60)) - SEARCH(""": """, A60) - 4)
	• Extracting text string between two of the same thing
		○ =SUBSTITUTE(MID(SUBSTITUTE("""" & B2&REPT(" ",6),"""",REPT(",",255)),2*255,255),",","")
		○ ["ABDOMEN"]
		○ Result: ABDOMEN
	• Highlight entire table and press Alt+o+a
	• How to covert seconds to HHMMSS
		○ =TEXT(INT(B2/3600),"00") & TEXT(INT(MOD(B2,3600)/60),"00") & TEXT(MOD(B2,60),"00")
