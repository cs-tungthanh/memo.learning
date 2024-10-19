# Overview
- **Offset** is like a page number (for each row)
	- degrade for large dataset, we need to scan through a significant portion of data to reach the staring point (page number) for the request
- **Cursor** is like a bookmark
## 6 types in pagination
### Offset-based
- `/orders?offset=0&limit=3`
- Pros:
	- Simple, works well for **static datasets** that don’t change frequently
	- Allow users to directly **jump** to a specific page.
- Cons: 
	-  **must to scan** all rows from the beginning to the requested row
		- Ex: you have 1 million rows and you want to read 10 records after 10,000 rows
			- -> you need to query from 0 -> 9,999 and return 10 records after it.
	- If **data frequently changes**, it can affect the result causing duplicate or missing rows.
		- If a new record is inserted before the offset, it might be missed or lead to duplicate entries.
- Example:
	- `SELECT * FROM posts ORDER BY created_at DESC LIMIT 10;`
	- `SELECT * FROM posts ORDER BY created_at DESC LIMIT 10 AND OFFSET 10;`
### Cursor-based
-  `/orders?cursor=xxx`
- @return: `{ data, nextCursor }`
- cursor(a unique identifier) to mark the position in the dataset
- **Example:** The first request might retrieve the first 10 posts and return a cursor value associated with the 10th post. The next request would include the previous cursor value to retrieve the next set of records after the 10th post.
-  Pros:
	- more efficient for large dataset
	- don't be affected by data changing
- Cons: hard to jump to a specific page because user cannot know the cursor value.
- Example:
	- `result = SELECT * FROM posts ORDER BY created_at DESC LIMIT 10;`
	- `SELECT * FROM posts WHERE created_at < result.created_at ORDER BY created_at DESC LIMIT 10;`
### Page-based
- `/orders?page=2&size=3`
- Specify the page number and page size
	- it has a little bit different with offset (page size is 1)
- Ex: 
	- page size is 3 items and return the second page
### Keyset-based
- `/orders?after_id=102&limit=3`
### Time-based
- `/orders?start_time=xxx&end_time=xxx`
### Hybrid
- It combines multiple pagination techniques: cursor + time,...
- Ex: /orders?cursor=xxx&start_time=xxx&end_time=xxx