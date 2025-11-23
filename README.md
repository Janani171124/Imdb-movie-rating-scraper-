# Imdb-movie-rating-scraper-
Automatic collection of movie rank, title,year,rating.
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from webdriver_manager.chrome import ChromeDriverManager
import pandas as pd
import time

# -----------------------------
# Setup Chrome WebDriver
# -----------------------------
options = webdriver.ChromeOptions()
options.add_argument("--start-maximized")

driver = webdriver.Chrome(
    service=Service(ChromeDriverManager().install()),
    options=options
)

# IMDb Top 250 URL
url = "https://www.imdb.com/chart/top/"
driver.get(url)
time.sleep(3)

# -----------------------------
# Scrape movie details
# -----------------------------
movie_rows = driver.find_elements(By.CSS_SELECTOR, ".ipc-metadata-list-summary-item")

titles = []
years = []
ratings = []

print("\n📌 IMDb Top 250 Movies:\n")

for index, row in enumerate(movie_rows[:250], start=1):

    # Title
    title = row.find_element(By.CSS_SELECTOR, "h3.ipc-title__text").text

    # Year
    metadata = row.find_elements(By.CSS_SELECTOR, ".cli-title-metadata-item")
    year = metadata[0].text if metadata else "N/A"

    # Rating
    rating = row.find_element(By.CSS_SELECTOR, ".ipc-rating-star--rating").text

    titles.append(title)
    years.append(year)
    ratings.append(rating)

    # Print on Terminal (VS Code)
    print(f"{index}. {title} ({year}) ⭐ Rating: {rating}")

# -----------------------------
# Create DataFrame
# -----------------------------
df = pd.DataFrame({
    "Rank": list(range(1, 251)),
    "Title": titles,
    "Year": years,
    "Rating": ratings
})

# -----------------------------
# Save to Excel file
# -----------------------------
excel_filename = "IMDb_Top_250.xlsx"
df.to_excel(excel_filename, index=False)

print("\n\n✅ Scraping Completed Successfully!")
print(f"📁 Excel File Saved As: {excel_filename}")

# Close browser
driver.quit()
