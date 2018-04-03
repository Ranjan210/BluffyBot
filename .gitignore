
var request= require('request');
var cheerio= require('cheerio');
var URL= require('url-parse');
var fs=require('fs');
var robotto =require('robotto');
var MongoClient=require('mongodb').MongoClient;
var url = "mongodb://localhost:27017/mydb";

var START_URL="https://www.unity3d.com";
var pagesVisited={};
var pagesToVisit=[];
var numPagesVisited=0;
var metas=[];
var options={
    headers:{
        'User-Agent':'SpiderFam'
    }
}
MongoClient.connect(url, function(err, db) {
    if (err) throw err;
    console.log("Database created!");
    db.close();
  });


var url=new URL(START_URL);
var baseUrl= url.protocol+"//"+url.hostname;
pagesToVisit.push(START_URL);
crawl();

function crawl()
{
    var nextPage=pagesToVisit.pop();
robotto.canCrawl(main,nextPage,
    function(err,isAllowed)
{
    if(err)
    {
        console.error(err);
        return;
    }
if(isAllowed){
    if(nextPage in pagesVisited){
        crawl();
    }
      else {
      VisitPage(nextPage,crawl);
      }
}else{
    crawl();
}
})

}




function VisitPage(url,callback){
    pagesVisited[url]=true;
    numPagesVisited++;

    console.log("Visiting page:" + url);

    request(url, function(error, response, body) {
     
        // Check status code (200 is HTTP OK)
        console.log("Status code: " + response.statusCode);
        if(response.statusCode !== 200) {
          callback();
          return;
        }
        
        // Parse the document body
        var $=cheerio.load(body);
     
        console.log("Page title:  " + $('title').text());
        collectInternalLinks($);
        callback();
      
      $("meta").each(function (index){
        var metat=$(this).attr('name');
        if(metat=="description"){
          var title=metat;
        var metatDescription=  $(this).attr('content');
            fs.appendFileSync('scraped.txt',title+'\n'+'\n'+metatDescription); 
        }
       
        
        });
    
    })
}

function collectInternalLinks($) {
    var relativeLinks = $("a[href^='/']");
    console.log("Found " + relativeLinks.length + " relative links on page");
    relativeLinks.each(function() {
        pagesToVisit.push(baseUrl + $(this).attr('href'));
    });
}

