
## JSON
- `jsonb`: using binary representation
- `json`: stores data as text

`jsonb` supports partial update
```go
db.Model(&User{}).Where("id = ?", 1).
    Update("info", gorm.Expr("info || ?", `{"age": 31}`))

```


## Bitmap
### Bitmap Index
- Bitmap index is a type of DB Index
	- using bitmap (**bit arrays**) to quickly locate rows in table satisfy the condition
- Pros
	- Speed up read query + compact (take less space than B-tree)
	- Suitable for **low cardinality column** (less unique value)
	- Fast for complex query: good for combining multiple condition using **bit-wise op (AND, OR)**
- Cons:
	- not ideal for frequency updates
		- More expensive to maintain than B-tree indexes if the table is write-heavy.
	- not great for high cardinality columns: IDs, timestamps,...

Example:

| Row # | Gender | Department |
| ----- | ------ | ---------- |
| 1     | M      | HR         |
| 2     | F      | HR         |
| 3     | M      | Sales      |
| 4     | M      | HR         |
| 5     | F      | Sales      |
| 6     | M      | Sales      |
| 7     | F      | HR         |

When we create bitmap for Gender, it will be like:
- Male: 1011010
- Female: 0100101

When we create bitmap for Department, it will be like:
- HR: 1101001
- Sales: 0010110

So when we query like
`select * from table where gender='M' and department='hr'
It will compute bit-wise operation: 1011010 X 1101001 = 1001000
so the row output will be: **row 1 and row 4**
Instead of scanning the whole table, the DB knows:  
👉 "I need to fetch the row at RID X" — jump directly to it.


### Problem:
Usecase: show banner based on user info (_Can **user X** see **banner Y**?_)
- You have a large number of user
- Each banner has a specific set of eligible users, based on dynamic rules.
- You need fast real-time access to banners a user can see
- Precomputing is allowable 

==> Using **Roaring bitmap**

let compare with **Traditional bitmap**
- Give
	- 1 million user IDs (0, .... 99999999..)
	- only 1 hundred user IDs can see banner X
- **Traditional Bitmap**
	- you must allocate **1 million bits** to store bitmap for **banner X**
	- 99% of bitmap is 0 (only 1 hundred bits are **1**) => huge waste
- **Roaring bitmap**
	- Internally splits the ID space into 2¹⁶ blocks (called containers).
	- Only allocates containers for **blocks that have users**.
	- Stores sparse blocks as **arrays**, dense blocks as **bitmaps**.
	- The same data might take just **a few MB or less**, depending on the distribution.
