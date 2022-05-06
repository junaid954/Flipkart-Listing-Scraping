# Flipkart Listing Data Creation and Analysis

![image](https://user-images.githubusercontent.com/61817305/167126198-05f76a2a-a0fa-41fa-943b-7ec4475a231a.png)


## Tools Used
* Python, Excel and Power BI

## Libraries Used
* Pandas, BeautifulSoup, requests, urllib and random

### Task
* Create data from scratch.
* Scrape the Flipkart Listing Data in Electronics category and create a DataFrame from it.

* Scraped Data includes - 

    - ASIN (Product unique identifier)
    - Category 
    - Any Offer on the product or not 
    - Title
    - Description of the Product
    - Total Ratings
    - Total Reviews
    - Total number of Stars
    - Product Price
    - Product Original Price
    - Discount offered on the Prouct
    - Free Delivery or not

* The data is cleaned in Python and Excel, post which further Transformation is done in Power BI.
* In the last stage we have crated an automated dashboard in Power BI which shows 100% correct results, which can be verified on the     Flipkart website.
* The biggest realization we found in the analysis is that Brands only offer discount if their product is not doing well in comparision to their competition.



### Step 1

Import BeautifulSoup
Make GET request to fetch page data
Parse HTML
Filter relevalnt Parts

1. To scrape the data from Flipkart website we are going to use the Python package called beautifulsoup.

To scrape the data we use request librairy. One of the librairy is called 'urllib'
We wil be making a get request to fetch the data from the website 
The web page contains HTML, CSS JAVASCRIPT and all of that comes to us via get request.
So similarly If we want to get a webpage in python we are going to make a get request using this librairy.
    (We are on a machine client and we want to access some data which is on the internet, so we are going to make a get request to the server > we hit a particular url and in return we get a HTTP response.)
Our task would be to make a get request and get some response and parse that response to extract the exact the data.

```
from bs4 import BeautifulSoup as soup
import pandas as pd
import requests
import urllib # given a web url we want to open that via urllib
import time
import requests, random
```
```
def getdata (url):
    user_agents = [
      "chrome/5.0 (Windows NT 6.0; Win64; x64",
      "chrome/5.0 (Windows NT 6.0; Win64; x32",
      "chrome/5.0 (Windows NT 6.1; Win64; x64",
      "chrome/5.0 (Windows NT 6.1; Win64; x32",
      "safari/5.0 (Windows NT 6.1; Win64; x64",
      "safari/5.0 (Windows NT 6.1; Win64; x32",
      'Mozilla/5.0',
      'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:77.0) Gecko/20100101 Firefox/77.0',
      'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.97 Safari/537.36',
      'Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:77.0) Gecko/20100101 Firefox/77.0',
      'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.97 Safari/537.36'
    ]
    user_agent = random.choice(user_agents) #Choose a random user_agent from the user_agents list
    header_ = {'User-Agent': user_agent}
```
- The HTTP headers User-Agent is a request header that allows a characteristic string that allows network protocol peers to identify the Operating System and Browser of the web-server
```
    header = { 'User-Agent' : 'chrome/5.0 (Windows NT 6.1; Win64; x64)' } 
    req = urllib.request.Request(url, headers=header_)
```
- The urllib.request module defines functions and classes which help in opening URLs (mostly HTTP), req will return a HTTP response which needs to be parsed.
```
    amazon_html = urllib.request.urlopen(req).read()  # we call the read method to read the HTTP data
```
- It basically contains the entire HTML of the Amazon page, there are different tags in it like div,h2,a,span,h3
- Now that we have fetched the page, the next part would be to parse the HTML, that's where Beautiful soup comes into picture  
- BeautifulSoup is a class and we import that above and now we will use the soup class to make a soup object which will help us to parse the HTML.
```
    a_soup = soup(amazon_html,'html.parser')
```
- soup() objects the html, we have to specify what kind of parsing we have to do: 
        - example: whether it's a json kind of data, whether it's HTML kind or something else.
        - In our case it's HTML kind of data so we give html parse as the second argument

###  Now our next task is to extract the relevant details form a_soup
```
    for e in f_soup.select('div[class="_13oc-S"]'):
```
<img width="960" alt="Screenshot_1" src="https://user-images.githubusercontent.com/61817305/164972090-4414ca04-c266-4945-bb99-573e25f814e9.png">

```
        try:
            asin = e.find('a',{'class':'_1fQZEK'})['href'].split('=')[1].split('&')[0]
        except:
            asin = 'No ASIN Found'
```
<img width="960" alt="Screenshot_2" src="https://user-images.githubusercontent.com/61817305/164972142-5fcb780f-844a-4652-a0c8-0fefe2e7654d.png">

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Example URL   
```
e.find('a',{'class':'_1fQZEK'})['href']   # RETURNS US THE BELOW URL
```
https://www.flipkart.com/apple-iphone-11-purple-64-gb/p/itm2b8d03427ddac?pid=MOBFWQ6BTFFJKGKE&lid=LSTMOBFWQ6BTFFJKGKE6U3GXK&marketplace=FLIPKART&q=iphone+11&store=tyy%2F4io&srno=s_1_1&otracker=search&otracker1=search&fm=Search&iid=2e09f9e6-247c-4182-ac50-32b92b031b55.MOBFWQ6BTFFJKGKE.SEARCH&ppt=sp&ppn=sp&ssid=nu86986eds0000001650783552221&qH=f6cdfdaa9f3c23f3   

Our aim is to get the unique identifier [ MOBFWQ6BTFFJKGKE ] within the url 

so we split the url on "=" which splits the url based on the " = " sign, notice that there are multiple "=" signs  so it will get splitted into multiple parts

This is how it will get splitted into:

 0. https://www.flipkart.com/apple-iphone-11-purple-64-gb/p/itm2b8d03427ddac?pid
 1. MOBFWQ6BTFFJKGKE&lid
 2. LSTMOBFWQ6BTFFJKGKE6U3GXK&marketplace
 3. FLIPKART&q
 4. 
 5. 
 .
 .
 12. f6cdfdaa9f3c23f3

But ! 
we are only interested in the first part that is the reason we specify [1] in the code  which will retrun us the below url.
Note: python indexing starts from 0

So by specifying [1] we get the below result:

MOBFWQ6BTFFJKGKE&lid

agai we will split this on "&" but this time we will return the [0] index which will return us the desired result.

MOBFWQ6BTFFJKGKE

-------------------------------------------------------------------------------------------------------------------------------------------------

In case the code doesn't find any results it will return 'No ASIN Found'  that's what the below code will return.
```
except:
            asin = 'No ASIN Found'
```
--------------------------------------------------------------------------------------------------------------------------------------------------
## Offer
```
        try:
            exchange = e.select('div._3xFhiH')[0].text
        except:
            exchange = 'No Offer'
```
<img width="960" alt="Screenshot_3" src="https://user-images.githubusercontent.com/61817305/164972177-f5b0edae-f711-4786-887d-1824278f5ebf.png">

---------------------------------------------------------------------------------------------------------------------------------------------------

## Bullet Points
```
 bullet_p = e.find('ul',{'class':'_1xgFaf'}).selec('li.rgWa7D') 
```
Basically we are getting the first ul with the class "_1xgFaf" and then getting li with the class "rgWa7D" within  the ul
```
bullet_p = [b.text for b in bullet_p] 
```
<img width="958" alt="Screenshot_4" src="https://user-images.githubusercontent.com/61817305/164972201-104a12ee-816d-472d-81bb-8d8e6305bd7e.png">

Then we are getting only the text from the li

- From our main div which we have named e we want to find ul with the class '_1xgFaf'
- Note: ".find " returns us the first instance of what we are trying to find. There can be many ul tags within the main div but .find returns the first one.
- Once we get the ul with the class '_1xgFaf' then we are selecting the <li> tag with the class "rgWa7D" ('li.rgWa7D') the '.' specifies the class here

The result will return us 

[<li class="rgWa7D">64 GB ROM</li>,
 <li class="rgWa7D">11.94 cm (4.7 inch) Retina HD Display</li>,
 <li class="rgWa7D">12MP Rear Camera | 7MP Front Camera</li>,
 <li class="rgWa7D">A13 Bionic Chip with 3rd Gen Neural Engine Processor</li>,
 <li class="rgWa7D">Water and Dust Resistant (1 meter for Upto 30 minutes, IP67)</li>,
 <li class="rgWa7D">Fast Charge Capable</li>,
 <li class="rgWa7D">Wireless charging (Works with Qi Chargers | Qi Chargers are Sold Separately</li>,
 <li class="rgWa7D">Brand Warranty of 1 Year</li>]
 
```
 [b.text for b in bullet_p]  # will return us 
```

- '64 GB ROM',
- '11.94 cm (4.7 inch) Retina HD Display',
- '12MP Rear Camera | 7MP Front Camera',
- 'A13 Bionic Chip with 3rd Gen Neural Engine Processor',
- 'Water and Dust Resistant (1 meter for Upto 30 minutes, IP67)',
- 'Fast Charge Capable',
-  'Wireless charging (Works with Qi Chargers | Qi Chargers are Sold Separately',
- 'Brand Warranty of 1 Year'


If no information is found return 'Information not showing up on the page'
```
        except:
            bullet_p = 'Information not showing up on the page'
```
------------------------------------------------------------------------------------------------------------------------------------------------------------
## Title 

First Notice the difference betweent the interface of the two pages:

<img width="959" alt="Screenshot_6" src="https://user-images.githubusercontent.com/61817305/164972261-e85916a0-d64f-4ebe-b835-5bed370c67d4.png">
<img width="960" alt="Screenshot_7" src="https://user-images.githubusercontent.com/61817305/164972270-a2b0b867-5222-42bc-b51a-5352d87b500c.png">

```
        try:
            title = e.find('div',{'class':'_4rR01T'}).text
```
<img width="958" alt="Screenshot_5" src="https://user-images.githubusercontent.com/61817305/164972283-3c4f3d90-5e6b-4a1e-9b87-dc6d41a3fd6d.png">

Find  the div with the class '_4rR01T' and return the text.
```
        except:
            title = e.find('a',{'class':'s1Q9rs'}).text
```
<img width="960" alt="Screenshot_8" src="https://user-images.githubusercontent.com/61817305/164972295-ae93d2db-7476-4ccc-9a3e-be1889476330.png">

The reason why we are specifying [e.find('a',{'class':'s1Q9rs'}).text] in the except is because the detail interface changes when we search for results like 
'earbuds', 'printer' etc.. so, even if the inerface changes we still will get the results for the title.
            

-----------------------------------------------------------------------------------------------------------------------------------------------------------
## Stars
```
        try:
            stars = e.find('div',{'class':'_3LWZlK'}).text
        except:
            stars = None
```
<img width="960" alt="Screenshot_9" src="https://user-images.githubusercontent.com/61817305/164972307-a49161d3-5681-48e4-8fed-0cb529ff527b.png">

 -----------------------------------------------------------------------------------------------------------------------------------------------------------

## Ratings
```            
        try:
            rating_count = e.find('span',{'class':'_2_R_DZ'}).text.split("&")[0]
        except:
            rating_count = None
 ```           
<img width="960" alt="Screenshot_11" src="https://user-images.githubusercontent.com/61817305/164972326-e2cb0065-a88b-4fb6-9a83-0ab024096957.png">

---------------------------------------------------------------------------------------------------------------------------------------------------------------

## Reviews
```
        try:
            review_count = e.find('span',{'class':'_2_R_DZ'}).text.split("&")[1]
        except:
            review_count = None
```
<img width="960" alt="Screenshot_11" src="https://user-images.githubusercontent.com/61817305/164972329-c636e566-df58-4726-bf51-3e611bdc9288.png">

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

## Listing Price 
```
        try:
            list_price = e.find('div',{'class':'_30jeq3 _1_WHN1'}).text
        except:
            list_price = 0
```  
<img width="917" alt="Screenshot_12" src="https://user-images.githubusercontent.com/61817305/164972340-affa3a0f-ebe6-4e61-b77e-7bf7dc6a9900.png">

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

## Original Price
```                                       
        try:
            original_price  = e.find('div',{'class':'_3I9_wc _27UcVY'}).text
        except:
            original_price = list_price
```
<img width="915" alt="Screenshot_13" src="https://user-images.githubusercontent.com/61817305/164972347-b97550ce-a350-4275-a2ac-879d30504234.png">

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

## Discount
```
        try:
            discount  = e.find('div',{'class':'_3Ay6Sb'}).text
        except:
            discount = 0
```
<img width="903" alt="Screenshot_14" src="https://user-images.githubusercontent.com/61817305/164972356-bbaa4ed7-8cc2-445e-a5c7-cfafd60d0708.png">

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

## Delivery
```
        try:
            delivery  = e.find('div',{'class':'_2Tpdn3'}).text
        except:
            delivery = 'No Free Delivery'
```

-----------------------------------------------------------------------------------------------------------------------------------------------------------------
```
                                       
cat = k
data =[]                                       
data.append({
            'ASIN': asin,
            'Category': cat,
            'exchange': exchange,
            'bullet_p':bullet_p,
            'Title':title,
            'Rating_Count':rating_count,
            'Review Count':review_count,
            'Stars':stars,
            'List_price':list_price,
            'Original_price':original_price,
            'Discount':discount,
            'Delivery': delivery
        })
        
    return f_soup
```
```                                       
def getnextpage(f_soup):
    
    try:
        page = f_soup.select_one('a[href*="&page="]:-soup-contains("Next")')['href']
```
First page will return 
- '/search?q=iphone&page=2'
page 2 will return 
- '/search?q=iphone&page=3'
and so on ..
.
.
```
        url =  'https://www.flipkart.com'+ str(page)
```
- After first page results are found the URL  will become https://www.flipkart.com/search?q=iphone&page=2
- Once we get second page results the URL will become https://www.flipkart.com/search?q=iphone&page=3
and so on . . 

```        
    except:
        url = None

    return url
```

If we found no URL retrun Nothing and return URL

--------------------------------------------------------------------------------------------------------------------------------------------------------
## Keywords

We pass the keywords as a list
```
keywords = ['iphone','mobile','earphone','earbuds','hard+drive','headphone','laptop','mixer+grinder',
          'monitor','powerbank','printer','refrigerator','router','smartwatch','speaker','camera',
            'tablet','tv','vaccume+cleaner','washing+machine','apple+watch','macbook','irons',
            'water+purifier','fan','air+cooler','inverter','sewing+machine','water+geyser',
           'ac','juicer+mixer','rice+cooker','dishwasher']
```

----------------------------------------------------------------------------------------------------------------------------------------------------------

```                                       
for k in keywords:
```
for every keyword in keywords 
```
    url = 'https://www.flipkart.com/search?q='+k
```
URL will become 
- https://www.flipkart.com/search?q='+iphone
- https://www.flipkart.com/search?q='+mobile

and so on .. 
```
    while True:
```
Till you find the last keyword do the run the below functions
``
        geturl = getdata(url)
```
- It will go to the top and run the function getdata(url)
- Once it it is finished running that function 
- The getdata(url) returns [ f_soup  ] 
- It will run the below function
- Which will accept the f_soup 
```
        url = getnextpage(geturl)
```
Go to the next page and return the URL of that page and give that to the getdata(url) function and this will continue till all results are found for a particular keyword and so on..
```       
        if not url:
            print('N 0   M O R E   R E S U L T S    F O U N D  F O R  T H I S  P A G E ')
            print('        S H O W I N G   N E X T  P A G E   R E S U L T S             ')
            break
        print(url)
```        

    
