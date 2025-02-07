### Documentation for the Streamlit Dashboard Code

This Python code is for a **Streamlit Dashboard** that allows the user to search for a specific term on Google and display the search result URLs in a table format. The program uses **Selenium** (with **Undetected ChromeDriver**) to automate the Google search, collect links from the search results, and display them using **Streamlit**. Here is a step-by-step explanation of how the code works:

---

### **Libraries Required:**
The following libraries are required to run this code:
1. **Streamlit**: For building the web dashboard.
   - `streamlit as st`
2. **Pandas**: For managing and displaying the search results in a table format.
   - `import pandas as pd`
3. **Selenium**: For web scraping, simulating user interaction on Google, and retrieving search results.
   - `import selenium`
4. **Undetected Chromedriver**: For bypassing detection and using Chrome to scrape Google search results.
   - `import undetected_chromedriver as uc`

### **Step-by-Step Code Explanation:**

#### 1. **Title and Dashboard Setup:**

```python
# Title of the Dashboard
st.title('Streamlit Dashboard for Google search result')

# Text input for search functionality
st.subheader("Search for data")
search_query = st.text_input("Enter search term:")
```
- `st.title` sets the main title of the Streamlit dashboard.
- `st.subheader` adds a subtitle.
- `st.text_input` creates an input field where the user can type a search term.

#### 2. **Submit Button:**

```python
# Submit button
submit_button = st.button("Search")
```
- `st.button` generates a button labeled "Search". When clicked, the action triggers the search functionality.

#### 3. **Google Search Functionality:**

The main function, `perform_google_search(search_query)`, is where the Google search is automated using **Selenium** and **Undetected ChromeDriver**.

```python
def perform_google_search(search_query):
    # Setup Undetected ChromeDriver
    options = uc.ChromeOptions()
    options.add_argument("--no-sandbox")
    options.add_argument("--disable-dev-shm-usage")
    options.add_argument("start-maximized")

    # Initialize the undetected driver
    driver = uc.Chrome(options=options)
```
- **Undetected ChromeDriver** is used to avoid detection when accessing Google.
- The `options.add_argument()` lines configure Chrome to run without sandbox restrictions and in a maximized window.

```python
    # Open Google Search
    driver.get("https://www.google.com")

    # Search for the query
    search_box = driver.find_element(By.NAME, "q")
    search_box.send_keys(search_query)
    search_box.send_keys(Keys.RETURN)  # Press Enter to submit the search
```
- This part opens **Google** using `driver.get()` and enters the search term (`search_query`) into the search box (`By.NAME, "q"`).

```python
    # Wait for the search results to load
    time.sleep(3)
```
- A `sleep(3)` is used to wait for the results to load before scraping the page.

#### 4. **Scrolling and Collecting Links:**

```python
    link_collection = []
    for i in range(3):  # Loop to iterate through the first 3 pages
        time.sleep(2)
        height = driver.execute_script('return document.body.scrollHeight')  # Get page height
        for i in range(0, height, 30):  # Scroll the page in increments of 30px
            driver.execute_script(f'window.scrollTo(0,{i});')
        time.sleep(2)
        
        # Collect the elements that contain the search results
        collect = driver.find_elements(By.CLASS_NAME, 'yuRUbf')  # These contain the links
        
        # Extract URLs from the 'href' attribute and add to list
        for item in collect:
            link = item.find_element(By.TAG_NAME, 'a').get_attribute('href')
            link_collection.append(link)
        
        # Click on the "Next" button to move to the next page
        next_xpath = '//*[@id="pnnext"]/span[2]'
        next_button = driver.find_element(By.XPATH, next_xpath)
        time.sleep(2)
        next_button.click()
```
- This part scrolls the page to load more results (if necessary) and collects the links from the search results (`By.CLASS_NAME, 'yuRUbf'`).
- The loop `for i in range(3)` allows the script to scrape the first three pages of results, collecting the links on each page.
- The script also clicks the **Next** button (`//*[@id="pnnext"]/span[2]`) to load the next page of results.

#### 5. **Return Collected Links:**

```python
    return link_collection
```
- The function returns the list `link_collection` containing the URLs of the search results.

#### 6. **Streamlit Output:**

```python
if submit_button and search_query:
    st.write(f"Searching for: {search_query}")

    # Perform the search and get results
    links = perform_google_search(search_query)

    if links:
        df = pd.DataFrame(links, columns=['Search results'])
        st.write("Search result", df)  # Displaying as a table
```
- When the user clicks the submit button (`submit_button`) and enters a search query, the script runs the `perform_google_search()` function to get the search result links.
- The search results are then displayed in a **pandas DataFrame** using `st.write(df)`. This renders the results in a table format.

#### 7. **Else Condition (Initial State):**

```python
else:
    st.write("Start by entering a search term and click 'Search'!")
```
- This part ensures that the message "Start by entering a search term and click 'Search'!" is displayed when the user hasn't entered a search term or clicked the "Search" button.

---

### **Flow of the Application:**

1. **User Input:**
   - The user enters a search term in the input box and clicks the "Search" button.
   
2. **Search Function:**
   - The program uses **Undetected ChromeDriver** and **Selenium** to search Google for the entered term.
   - The program collects links from the first three pages of Google search results.

3. **Display Results:**
   - The search results (URLs) are displayed in a table format using **pandas** and **Streamlit**.

4. **Output:**
   - The search results are presented as a table in the Streamlit dashboard for the user to view.

---

### **How to Run:**
1. Install the necessary libraries:
   ```bash
   pip install streamlit pandas selenium undetected-chromedriver
   ```
   
2. Save the code into a `.py` file (e.g., `google_search_dashboard.py`).

3. Run the Streamlit app:
   ```bash
   streamlit run google_search_dashboard.py
   ```

---




