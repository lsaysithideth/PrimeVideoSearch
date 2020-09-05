# PrimeVideoSearch

There are so many options today when it comes to shows and TVs to watch.  Sometimes just scrolling through all the titles can be exhausting and, before you know it, you have spent an hour trying to find the next show to binge.  

This Python code will automate the organization of movie and show data into a nice, neat csv file where you can compare many titles on Amazon Prime Video in an instant.  

Navigation
Part 1: Set-up packages for web scraping
Part 2: Find CSS tags to input into python code
Part 3: Write python code to importing packages, prepare variables and define functions 
Part 4: Finish python code to trigger functions and save data to csv file

Part 1: Set-up packages for web scraping

We are using the Python3 environment on Mac, however the majority of these steps will work on Windows as well.  I’m also using Visual Code Studio as my IDE or editor, but you can use other IDEs.

Instructions for Installing Chrome Driver
Separate from Python, you will need the following downloads installed:
•	Google Chrome internet browser
•	ChromeDriver

You can install ChromeDriver from the https://chromedriver.chromium.org site and choose the correct version based on your Chrome browser version.  You can find your Chrome browser version by going into Google Chrome > Help > About Google Chrome and find the version there.

 

Once you have ChromeDriver downloaded, unzip the folder and move the chromedriver file to /usr/local/bin.

Double-click to open the chromedriver file to initialize it.

Troubleshooting:
You may run into an error message below.

 

To resolve this, go to System Preferences > Security & Privacy > General, then click the lock to make changes.

Click Open Anyway beside where it says “chromedriver” was blocked.

 

Then click Open to proceed.
 

Click the lock again to prevent further changes.

Go back to the bin folder and double-click chromedriver again to initiate.  This time a window will pop up with the last line of text saying:

ChromeDriver was started successfully.

You can then close the window.


Python Package Installation
Make sure the following Python packages are installed.

•	pandas - will allow us to save data to a csv, text, or excel file
•	selenium – will allow us to control a webpage using CSS tags
•	bs4 – will format and parse page source data neatly

You can install the packages with the pip installer by running the following example code in the Mac Terminal (find Terminal by pressing CMD + SPACE and typing “Terminal” in the search bar on your Mac):

python3 -m pip install pandas –user

When the install finishes you should receive a message similar to below if you ran the code above:

Successfully installed numpy-1.19.1 pandas-1.1.1 python-dateutil-2.8.1 pytz-2020.1

Part 2: Find CSS tags to input into python code

First, we need to direct Python to automatically find each link for each movie or show.  To do that, we will need to find the CSS tag that holds all the individual movie and show links.

Get Movie and Show Link Tags
The site I want to gather information from is:

https://www.amazon.com/gp/video/storefront/

1.	Open this link up in your Chrome browser.
2.	Press F12 to open the Developer Tools to reveal the CSS Code.
3.	Click the top left button in the Developer Tools to enable inspecting elements.
 
4.	Start clicking on a movie title.  Notice it will highlight certain lines of code on the right pane.  We are looking for something like below:

 

We know this class holds movie links because within the tag (in the <> brackets) there is a “href=” tag that specifies the correct website links in HTML/CSS language.  The link for the element we inspected is: 

/gp/video/detail/B08DKPFFBC/ref=atv_hm_hom_1_c_Yzm1i7_brws_3_6

In this case, this is a partial link that is appended to the main website, which is 

https://www.amazon.com

If we combine the two, we would get

https://www.amazon.com/gp/video/detail/B08DKPFFBC/ref=atv_hm_hom_1_c_Yzm1i7_brws_3_6
	
Now if we test this link in the browser, you will see it directs to the correct individual site for that particular movie or show where we can find all the details we need.  

 

All the titles will be set up like this with /gp/video/detail/ in the link which we will input in our code. 

Get Title Details Tags
Now that we have the link tags ready, we’ll have to instruct Python on where exactly on the webpage we want Python to extract information from.  Looking at a particular movie or show, we want to extract the title, ratings, and synopsis.

 

Inspect each part the same way as the previous steps, but this time we are looking for (Yellow highlighted means we will use this in our Python code and Blue highlighted shows what will be recorded into our csv files):

Title: <h1 class="_1GTSsh _2Q73m9" data-automation-id="title">7500</h1>

Rating: <span class="XqYSS8 …="IMDb Rating 6.3">6.3</span></span>

Synopsis: <div class="_3qsVvm _1wxob_">When terrorists try to seize control of a Berlin-Paris flight, a soft-spoken young American co-pilot struggles to save the lives of the passengers and crew while forging a surprising connection with one of the hijackers.</div>

NOTE: Some class names require full string in the quotes and some only require the first part before the space.  You might have to test out the code to see which returns the correct data.

Part 3: Write python code to importing packages, prepare variables and define functions

Now that we have our programs and CSS tags set up, it’s finally time to write some python code!

Let’s start off with importing the correct packages:

from selenium import webdriver
from bs4 import BeautifulSoup
import pandas as pd

Next we’ll define our variables:

#create empty lists to be used in functions later
vidLink = [] 
title = []
rating = []
synopsis = [] 

#define CSS variables
titleClass = "h1"
titleName = "_1GTSsh _2Q73m9"
ratingClass = "span"
ratingName = "XqYSS8 AtzNiv"
synopsisClass = "div"
synopsisName = "_3qsVvm _1wxob_"

#Store URLs and Substrings known to use in code
storeFrontURL = "https://www.amazon.com/gp/video/storefront/"
vidDtlSubStr = "/gp/video/detail/"

Next let’s define our function:

#This function finds the specific text to extract based on the CSS tags specified
def scrapText(lst,classType,className):
    findClass = soup.find_all(classType,class_=className)
    if len(findClass) == 0:
        lst.append("Unknown") #helps fill in data that can't be found
    else:
        for n in findClass:
            lst.append(n.text)

Part 4: Finish python code to trigger functions and save data to csv file

Next we’ll put in code that initializes a Chrome browser to be controlled by Python using the selenium packages and point it to the storefront website:

#Next initialize browser to be controlled by Python below
#DO NOT CLOSE new browser window that is opened until end of code run.  
# Make sure chromedriver is installed beforehand
driver = webdriver.Chrome("/usr/local/bin/chromedriver") 
driver.get(storeFrontURL)

Next we’ll find the URLs of each title:

#Find all URL links on website and append them in the vidLink list
elems = driver.find_elements_by_xpath("//a[@href]")
for elem in elems:
    if vidDtlSubStr in elem.get_attribute("href"):
        vidLink.append(elem.get_attribute("href"))

We will then start scraping the individual titles’ data. Note that we start with 5 titles at first as increasing too many titles can cause performance lags.

#start scraping the data for each link
for i in range(0,5): #control for performance
    driver.get(vidLink[i])      
    content = driver.page_source
    soup = BeautifulSoup(content)

    scrapText(title,titleClass,titleName)
    scrapText(rating,ratingClass,ratingName)
    scrapText(synopsis,synopsisClass,synopsisName)

Lastly, we will print the data to a csv file named “PrimeVid.csv”:

#Save the data into neat columns in a csv file
a = {'Title':title,'Rating':rating, 'Synopsis':synopsis}
dfA = pd.DataFrame(a)
dfA.to_csv('PrimeVid.csv', index=False, encoding='utf-8')
