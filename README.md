from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time

driver = webdriver.Chrome()
wait = WebDriverWait(driver, 15)

try:
    # Test 1: Open Myntra
    driver.get("https://www.myntra.com")
    driver.maximize_window()
    wait.until(EC.presence_of_element_located((By.CLASS_NAME, "desktop-searchBar")))
    print("✅ Test 1 Passed: Myntra homepage loaded successfully")

    # Test 2: Search for shoes
    search_box = driver.find_element(By.CLASS_NAME, "desktop-searchBar")
    search_box.send_keys("shoes")
    search_box.send_keys(Keys.RETURN)
    wait.until(EC.presence_of_element_located((By.XPATH, "(//li[contains(@class,'product-base')])[1]")))
    print("✅ Test 2 Passed: Search results for 'shoes' loaded")

    # Test 3: Click on first product
    driver.find_element(By.XPATH, "(//li[contains(@class,'product-base')])[1]").click()
    time.sleep(2)

    # Test 4: Switch to new tab
    driver.switch_to.window(driver.window_handles[1])
    wait.until(EC.presence_of_element_located((By.XPATH, "//h1[@class='pdp-title']")))
    print("✅ Test 3 & 4 Passed: Product page opened in new tab")

    # Test 5: Extract product title and price
    title = driver.find_element(By.XPATH, "//h1[@class='pdp-title']").text
    price = driver.find_element(By.XPATH, "//span[@class='pdp-price']").text
    print("✅ Test 5 Passed: Product Title and Price Extracted")
    print(f"🛍️ Product Title: {title}")
    print(f"💰 Product Price: {price}")

    # Select size if available
    try:
        size = wait.until(EC.element_to_be_clickable((By.XPATH, "//div[@class='size-buttons-buttonContainer']/div[1]")))
        size.click()
        print("✅ Size selected")
    except:
        print("⚠️ No size selection required or already selected")

    # Close any popup
    try:
        close_btn = driver.find_element(By.CLASS_NAME, "myntraweb-sprite.popup-close")
        close_btn.click()
        print("⚠️ Closed interfering popup.")
    except:
        pass

    # Test 6: Add to Bag
    print("🟡 Trying to click ADD TO BAG...")
    try:
        add_btn = wait.until(EC.presence_of_element_located(
            (By.XPATH, "//div[contains(translate(text(), 'abcdefghijklmnopqrstuvwxyz', 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'),'ADD TO BAG')]")
        ))
        driver.execute_script("arguments[0].scrollIntoView({block: 'center'});", add_btn)
        time.sleep(1)
        driver.execute_script("arguments[0].click();", add_btn)
        print("✅ Test 6 Passed: Product added to bag")
    except Exception as e:
        print(f"❌ Test 6 Failed: Could not click ADD TO BAG - {e}")



finally:
    time.sleep(3)
    driver.quit()
