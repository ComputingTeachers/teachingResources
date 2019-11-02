#Chain Reaction Resource Generator

## Categories
- revision
- group work
- classroom activity
- question and answer

## Summary
This resource generates Chain Reaction question cards from a set of question:answer pairs.

Each student receives one or more cards.  The cards each have an answer (displayed prominently) and a question.  The starting card's question is read, and all the students look to their own cards for the correct answer.  The student who has that answer, states it and then asks the question on that card.  The next answer is found and so forth.

A chain reaction question card set could be used as an in class activity, but also students could create their own question sets for group revision.

## Resource
The code included below is from a Google Sheets macro.  Google Sheets was used because the school involved ran GSuite and so all students and staff would have access to Google Sheets.  The school did not have a programming curriculum, so this also kept the resource accessible.

### Code

```
/** @OnlyCurrentDoc */

function setupToPrint() {
  var spreadsheet = SpreadsheetApp.getActive();
  var data = spreadsheet.getActiveRange();
  var questionSheet = spreadsheet.getActiveSheet();
  var printSheetName = "print" + questionSheet.getName();
  var questionOverflowLimit = 1000;

  var questionCol = "A";
  var answerCol = "B";
  var firstQuestionRow = 2;
  var lastQuestionRow = firstQuestionRow;
  var printStartRow = 1;

  var printIdCol = 1;
  var printTextCol = 2;
  var questionID = "Q";
  var answerID = "A";

  //creates a unique random id each time
  var randomIds = [];
  function randomIDGenerator(){
    var r = Math.floor(Math.random()*questionOverflowLimit);
    r = r + questionOverflowLimit;
    if(randomIds.indexOf(r) == -1){
      randomIds[randomIds.length] = r;
      return r;
    }
    else{
      return randomIDGenerator();
    }
  }

  //find number of questions
  while(lastQuestionRow < questionOverflowLimit){
      if(questionSheet.getRange(lastQuestionRow, 1).isBlank()){
        lastQuestionRow --;
        break;
      }
      lastQuestionRow++;
  }
  var numQuestions = lastQuestionRow - firstQuestionRow;
  Logger.log(numQuestions);


  //link onto new sheet
  var printRow = printStartRow + 1;
  var printSheet = spreadsheet.insertSheet(printSheetName);
  for (var q = firstQuestionRow; q<= lastQuestionRow; q++){
    //generate random ID to match the question and answer
    var id = randomIDGenerator();
    //question
    printSheet.getRange(printRow, printIdCol).setValue(questionID + id);
    printSheet.getRange(printRow, printTextCol).setValue("=\'" + questionSheet.getName() + "'\!" + questionCol + q);

    //answer
    if(q == lastQuestionRow){
      printRow = printStartRow;
    }
    else{
      printRow = printRow + 2;
    }
    printSheet.getRange(printRow, printIdCol).setValue(answerID + id);
    printSheet.getRange(printRow, printTextCol).setValue("=\'" + questionSheet.getName() + "'\!" + answerCol + q);

    printRow++;

  }

  //format for printing

  var printEndRow = printStartRow + 3 * numQuestions + 1;
  Logger.log(printEndRow);

  spreadsheet.getActiveSheet().setColumnWidth(printIdCol, 55);
  spreadsheet.getActiveSheet().setColumnWidth(printTextCol, 747);
  spreadsheet.getActiveSheet().setRowHeights(printStartRow, printEndRow, 63);

  spreadsheet.setActiveRange(printSheet.getRange(printStartRow, printIdCol, printEndRow-printStartRow + 1, printTextCol-printIdCol + 1));
  spreadsheet.getActiveRangeList().setBorder(false, false, false, false, false, false);
  spreadsheet.getActiveRangeList().setVerticalAlignment('middle').setFontSize(14);
  spreadsheet.getActiveSheet().setColumnWidth(2, 747);

  for(var i = printStartRow; i<=printEndRow; i++){
    if((i-printStartRow)%3 == 0){//answer rows
      printSheet.getRange(i, printTextCol).setFontWeight('bold').setFontSize(16).setHorizontalAlignment("center").setWrapStrategy(SpreadsheetApp.WrapStrategy.WRAP).setFontFamily("Arial");

      printSheet.getRange(i+1, printTextCol).setWrapStrategy(SpreadsheetApp.WrapStrategy.WRAP).setFontFamily("Arial");

      printSheet.getRange(i, printIdCol, 2,2).activate();
      printSheet.getActiveRangeList().setBorder(true, true, true, true, null, null, '#000000', SpreadsheetApp.BorderStyle.SOLID);
    }
  }

  spreadsheet.setActiveRange(printSheet.getRange(printStartRow, printIdCol, printEndRow-printStartRow + 1, 1));
    spreadsheet.getActiveRangeList().setFontSize(10);


};
```

### How to use this resource

### Future Development