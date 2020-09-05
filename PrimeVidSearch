from selenium import webdriver
from bs4 import BeautifulSoup
import pandas as pd

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

#This function finds the specific text to extract based on the CSS tags specified
def scrapText(lst,classType,className):
    findClass = soup.find_all(classType,class_=className)
    if len(findClass) == 0:
        lst.append("Unknown") #helps fill in data that can't be found
    else:
        for n in findClass:
            lst.append(n.text)

#Next initialize browser to be controlled by Python below
#DO NOT CLOSE new browser window that is opened until end of code run.  
# Make sure chromedriver is installed beforehand
driver = webdriver.Chrome("/usr/local/bin/chromedriver") 
driver.get(storeFrontURL)

#Find all URL links on website and append them in the vidLink list
elems = driver.find_elements_by_xpath("//a[@href]")
for elem in elems:
    if vidDtlSubStr in elem.get_attribute("href"):
        vidLink.append(elem.get_attribute("href"))

#start scraping the data for each link
for i in range(0,5): #control for performance
    driver.get(vidLink[i])      
    content = driver.page_source
    soup = BeautifulSoup(content)

    scrapText(title,titleClass,titleName)
    scrapText(rating,ratingClass,ratingName)
    scrapText(synopsis,synopsisClass,synopsisName)

#Save the data into neat columns in a csv file
a = {'Title':title,'Rating':rating, 'Synopsis':synopsis}
dfA = pd.DataFrame(a)
dfA.to_csv('PrimeVid.csv', index=False, encoding='utf-8')