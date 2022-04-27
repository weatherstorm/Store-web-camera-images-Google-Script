# Store-web-camera-images-Google-Script
A Google Script that grabs webcam images via URL every 5 minutes and stores them in Google Drive. There is also a function to remove images after 14 days. 

A simple Google script that downloads a web camera images via URL to your Google Drive. There is also a function that will delete old image folders after 14 days. This all can be customizable to fit your needs.  


//Grabs webcam image from the website and stores it in my Google Drive
function getWebcam() {
  var d = new Date()
  var timeStamp = d.getTime()
  var timeZone = Session.getScriptTimeZone();
  var dString = Utilities.formatDate(d, timeZone, 'yyyy-MM-dd')

  var url = "https://www.centuryfarmweather.com/webcam/image.jpg"

  var response = UrlFetchApp.fetch(url).getBlob()

  //set file name to unix timestamp
  response.setName(timeStamp)
 
  var folder = DriveApp.getFolderById("1XyO7DUh6Ty1mt27ernO0G7vtXxWQwmOQ")
  
  //check if folder exists
  var folderName = dString
  
  // Log the name of every folder in the user's Drive that you own and is starred.
  var folders = DriveApp.getFoldersByName(folderName)
  //Logger.log(folder)

  if(folders.hasNext() == false){
    folder = folder.createFolder(dString)
    return folder.createFile(response)
  }else{
    folder = folders.next()
    //Logger.log(folder)
    return folder.createFile(response)
  }
}

//Delete 2 week old files
function cleanDrive() {

  var folder = DriveApp.getFolderById("1XyO7DUh6Ty1mt27ernO0G7vtXxWQwmOQ")

  var folderNames = folder.getFolders()

  //timestamp for two weeks out
  const MILLIS_PER_DAY = 1000 * 60 * 60 * 24
  const d = new Date()
  var old = new Date(d.getTime() - 14 * MILLIS_PER_DAY)
  var timeZone = Session.getScriptTimeZone();
  var dateString = Utilities.formatDate(old, timeZone, 'yyyy-MM-dd')

  while (folderNames.hasNext()){
    var folders = folderNames.next()
    var fNdate = new Date(folders)
    var dSdate = new Date(dateString)
    //Logger.log(dateString)
    //Logger.log(dSdate)

    if(folders < dateString){
      folders.setTrashed(true)
    }
  
  }

}
