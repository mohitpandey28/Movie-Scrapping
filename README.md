## Project Title:Rotten Tomatoes Movie Information Scraper

Description:

This Python script leverages Selenium, a web automation tool, to scrape movie information from the Rotten Tomatoes website.

The script targets a list of movies and retrieves various details such as audience counts, audience scores, Tomatometer scores, and additional movie information.

Features:

Automated Scraping: The script automates the process of navigating the Rotten Tomatoes website and extracting movie information.

Dynamic Content Handling: It handles dynamic content loading and waits for elements to be clickable before interacting with them.

Customizable Movie List: Users can provide a list of movie titles to scrape information for.

Data Extraction: Extracts key movie details including audience counts, audience scores, Tomatometer scores, and additional information.

Robustness: Designed to handle potential errors or delays, ensuring smooth execution even in less predictable web environments.

Usage:

Setup Environment: Ensure Python is installed along with required dependencies such as Selenium and Pandas.

Configure WebDriver: Download the appropriate browser driver (e.g., ChromeDriver) and set the path in the script.

Run the Script: Execute the Python script (rotten_tomatoes_scraper.py).

View Results: The scraped movie information is stored in a Pandas DataFrame for further analysis or export.


## code 

from selenium import webdriver

from selenium.webdriver.chrome.options import Options

from selenium.webdriver.common.by import By

from selenium.webdriver.common.keys import Keys

from selenium.webdriver.support.ui import WebDriverWait

from selenium.webdriver.support import expected_conditions as EC

from time import sleep

website = 'https://www.rottentomatoes.com'

path = "/Users/apple/Downloads/chromedriver-mac-x64"

### Create ChromeOptions object
chrome_options = Options()

### Set the executable path through binary_location
chrome_options.binary_location = path

### Create WebDriver instance with ChromeOptions
driver = webdriver.Chrome(options=chrome_options)
driver.get(website)

movies_list = ["War","Super 30","Mohenjo Daro"]
movie_names =[]
audience_counts = []
audience_scores = []
tomatometers =[]
movie_infos = []

for subject in movies_list:
    search_path = f"//a[contains(text(),'{subject}')]"

    search_box = driver.find_element(By.XPATH, "//input[@class='search-text']")
    search_box.clear()
    search_box.send_keys(subject)
    search_box.send_keys(Keys.ENTER)
    sleep(5)

    # Introduce a wait for the movies tab to be clickable
    movies_tab_locator = (By.XPATH, "//*[@id='search-results']/nav/ul/li[3]/span")
    movies_tab = WebDriverWait(driver, 10).until(EC.element_to_be_clickable(movies_tab_locator))
    movies_tab.click()

    # Use WebDriverWait to wait for the element to be clickable
    my_movies_locator = (By.XPATH, search_path)
    my_movies = WebDriverWait(driver, 10).until(EC.element_to_be_clickable(my_movies_locator))

    if my_movies:
        movie_names.append(my_movies.text)
        my_movies.click()
        sleep(3)

        audience_count = driver.find_element(By.XPATH, "//a[@slot='audience-count']").text
        audience_counts.append(audience_count)
        
        audience_element = driver.find_element(By.XPATH,"//score-board-deprecated")
        audience_score =  audience_element.get_attribute('audiencescore')
        audience_scores.append(audience_score if audience_score else "N/A")

        tomatometer_element = driver.find_element(By.XPATH, "//score-board-deprecated")
        tomatometer_score = tomatometer_element.get_attribute('tomatometerscore')
        tomatometers.append(tomatometer_score if tomatometer_score else "N/A")

        movie_info = driver.find_element(By.XPATH, "//ul[@id='info']").text.split('\n')
        movie_infos.append(movie_info)

        print(subject, "successful")
        sleep(3)
    else:
        print("not successful")

import pandas as pd
cat = pd.DataFrame(zip(movie_names,audience_counts,audience_scores,tomatometers,movie_infos)
                  ,columns=['movie_names','audience_counts','audience_scores','tomatometers','movie_infos'])





