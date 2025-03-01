# Simple ICU Cabinet Inventory Documentation
## Overview
This repository manages the inventory tracking of ICU cabinet supplies using an Excel-based system. The inventory sheet records the expiration dates of medical items and their availability status.

## File Structure
Simple ICU Cabinet Inventory.xlsx: The main inventory tracking file.

## Columns Explanation
**ITEM NAME:** Name of the medical supply.  
**EXPIRATION DATE (JANUARY - DECEMBER):** Expiration dates of items for each month.  
**LAST EXPIRATION:** The latest expiration date recorded.  
**NEAREST EXPIRATION:** The closest upcoming expiration date.  
**DAYS LEFT TILL EXPIRATION:** Number of days remaining before expiration.  
**TOTAL DAYS LEFT:** Overall remaining days before expiration.  
**STATUS:** Indicates stock availability (e.g., "No Stocks / Last Day").  

## Formulas Used
### 1️⃣ Last Expiration
     =ARRAY_CONSTRAIN(ARRAYFORMULA(IFERROR(IF(MAX((B6:M6<$R)*B6:M6)=0,"",MAX((B6:M6<$R)*B6:M6))),""))), 1, 1)  
* If no past expiration exists, returns an empty string.  
* This formula finds the most recent past expiration date (i.e., the latest date before today's date).  
* B6:M6 represents a row range containing expiration dates for different months.  
* (B6:M6<$R)*B6:M6:  
     * This checks which dates in the range are before today ($R stores today’s date).  
     * It multiplies by B6:M6 to keep valid dates and ignore invalid ones.
* MAX(...):  
     * Extracts the latest expired date before today.  
     * If no past expiration exists, it returns an empty string "".  
* ARRAYFORMULA: Ensures the formula applies across multiple rows dynamically.  
* ARRAY_CONSTRAIN(..., 1, 1): Restricts output to just one cell.  
### Purpose:  
✅ Identifies the last expiration date before today for each inventory item.

### 2️⃣ Nearest Expiration
     =ARRAY_CONSTRAIN(ARRAYFORMULA(IF(MIN(IF(B6:M6>$R,B6:M6))=0,"",MIN(IF(B6:M6>$R,B6:M6)))), 1, 1)  
* This formula finds the closest upcoming expiration date after today.  
* MIN(IF(B6:M6>$R,B6:M6)):  
     * Filters out expiration dates after today ($R).  
     * Finds the earliest future expiration date.  
* If there are no valid future dates, it returns "".  
### Purpose:
✅ Identifies the next expiration date for each item, helping prioritize stock monitoring.

### 3️⃣ Days Left Till Expiration
     =ARRAY_CONSTRAIN(ARRAYFORMULA(IFERROR(IF(O6,O6-$R$1,""),"")), 1, 1)  
* Calculates the days remaining until the nearest expiration date.  
* O6 is assumed to store the nearest expiration date (from previous formula).  
* O6 - $R$1:  
     * Subtracts today’s date ($R$1) from the nearest expiration date.
     * This gives the number of days left.
* IFERROR(..., ""): Prevents errors if O6 is empty.
### Purpose:
✅ Helps track how much time is left before an item expires.

### 4️⃣ Total Days Left
     =ARRAY_CONSTRAIN(ARRAYFORMULA(IFERROR(IF(MAX(B6:M6-$R$1)<0,IF(MAX((B6:M6<$R$1)*B6:M6)=0,0,MAX((B6:M6<$R$1)*B6:M6)-$R$1),IF(MAX(IF(B6:M6>$R$1,B6:M6))=0,0,MAX(IF(B6:M6>$R$1,B6:M6))-$R$1))),"NO EXPIRY")), 1, 1)  
* This formula calculates the total number of days left for an item before its expiration by checking both past and future expiration dates.  

### Break down:
`MAX(B6:M6-$R) < 0`  
* Checks if all expiration dates are in the past (i.e., expired).  
* If true, the formula proceeds to find the last expiration date.  
  
**Handling Past Expiration:**  
`IF(MAX((B6:M6<$R)*B6:M6)=0,0,MAX((B6:M6<$R)*B6:M6)-$R)`
* Finds the last expired date before today ($R).  
* If there are no past expiration dates, it returns 0.  
* Otherwise, it calculates the difference (last expiration - today).  
  
**Handling Future Expiration:**  
`IF(MAX(IF(B6:M6>$R,B6:M6))=0,0,MAX(IF(B6:M6>$R,B6:M6))-$R)`  
* If there is a future expiration date, it returns the number of days left.
* If there are no future expiration dates, it returns 0.

**Error Handling `(IFERROR)`**  
* If there’s an issue (e.g., empty or invalid values), it returns "NO EXPIRY".  
  
**ARRAY_CONSTRAIN(..., 1, 1)**  
* Ensures only one value is returned per row, preventing unnecessary calculations.  

### Purpose:
✅ Determines how many total days remain before an item expires.  
✅ Handles cases where all expiration dates are in the past or no expiry exists.  
✅ Ensures robust error handling and prevents blank outputs.  

### 5️⃣ Status
     =IF(R6<0,"EXPIRED",IF(R6=0,"NO STOCKS / LAST DAY",IF(R6<30,"NEAR EXPIRY",IF(R6="NO EXPIRY","NO EXPIRY","VALID"))))))

Categorizes inventory items based on expiration risk:
* EXPIRED: If the item has already expired.
* NO STOCKS / LAST DAY: If the expiration is today.
* NEAR EXPIRY: If the item will expire within 30 days.
* NO EXPIRY: If the item does not have an expiration date.
* VALID: If the item is still good for more than 30 days.

## Usage Guide
Updating Inventory:  
* Add new items under the ITEM NAME column.
* Fill in expiration dates in the respective monthly columns.
* The sheet automatically calculates expiration status.  

Monitoring Expiry Dates:
* Check the NEAREST EXPIRATION column for the closest expiration date.
* Use the STATUS column to track items that are low in stock or expired.

Maintaining Stock Levels:
* If an item is out of stock, update the STATUS field accordingly.
* Ensure that expired items are removed or replaced.
