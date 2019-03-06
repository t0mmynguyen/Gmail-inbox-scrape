function processUnreadEmails() {

    //exclude emails that SHOULD NOT be signed up in ET
    var excludeEmailsArr = ["apps-scripts-notifications@google.com"];
    
    //This queries Gmail inbox for unread emails in the 'inbox' folder, limited to 'new'
    var threads = GmailApp.search('is:unread in:inbox' , 0, 10);
    
    //Loop over the inbox emails
    for (var i = 0; i < threads.length; i++) { 
    
      //Extract the message and 'from' line
      var message = threads[i].getMessages()[0]; 
      var fromAddress = message.getFrom();
      
      //Filter the actual email address from the from line e.g. Example <example@gmail.com>
      fromAddress = fromAddress.match(/\S+@\S+\.\S+/g);    
      if ( fromAddress.length ) {
       
        var validEmail = fromAddress[0];
        validEmail = validEmail.replace(">", "");
        validEmail = validEmail.replace("<", "");
       
        //Added 2 lines of code to replace double quotes with "nothing"
        validEmail = validEmail.replace("\"", "");
        validEmail = validEmail.replace("\"", "");
                                        
       //Log the email we have found
       Logger.log('========= ' + validEmail + ' =========');
        
        //check to make sure validEmail is NOT in excludeEmailsArr (exclusion array)
        if ( excludeEmailsArr.indexOf(validEmail) == -1 ) {
          //Build signup string
          var signupLink = 'http://api.example.com/api/v1/jsonp/et/subscribers/add?callback=Request.JSONP.request_map.request_1&email_address=' + validEmail + '&source=Example';
          
          Logger.log(signupLink); 
          
          //Http request to signup api
          var response = UrlFetchApp.fetch(signupLink);
          //Logger.log(response.getContentText());
        } 
        
      }
      
      //Finish by marking email as read
    threads[i].markRead();
    }
