---
tags:
  - bloomfilter
  - bitmaps
  - roaring-bitmaps
---

# Bloom-filter
- check existence of an item in set - allow **false positive**
- using **a bit array of size m** and **k hash functions**
- Redis is supported bloom-filter
- Answer questions like
	- Has user purchased in this category/product/services?
	- Do I need to skip some security check with this transaction?
	- Has this credit card been reported as stolen/lost?
	- Has this user seen this ad?
	- check a user name is taken?
- False positive means: it says this username is taken but actually not
### why false negative is impossible
banana
hash1(banana) -> index 1
hash2(banana) -> index 3
hash3(banana) -> index 100

010100..1
it never set 1->0 so if banana occurs it always sit there
### what happen when we increase the number of hash function
- reduce collision - false positive rate decrease
	- it's harder for a random element to match all required bits 
- but false positive increase
	- because it set 1 to more bit at overall - make the bit array full quickly

# Roaring bitmap
- it is an exact data structure that store compressed bitmaps 
	- It stores the actual values in compressed way - not a hashed approximation like Bloom filter.
	- unlike bloom filter is probabilistic 
	- it doesn't have False positive - bloom filter say it may be exist
- for tradition bitmaps we have memory issue when data is sparse 
	- for example we have user 1 and user 1000, despite of having only 2 users but we still need a bitmaps of size 1000 bits to store it
	- 01........1
- For roaring bitmaps it splits into each container and use diff compression strategy depend on density that make more memory efficient but still providing O(1) look up

# Question
## Why don't use only roaring bitmap rather than Bloom filter
- Bloom filter is much more memory efficient by not storing the actual value
- Bloom filter is often used as a first-layer filter
	- Request -> Bloom filter-> Roaring bitmap -> Database
Example use cases:
- cache filtering
- database read optimization
- preventing unnecessary disk lookup
- **Roaring Bitmap is better when correctness is critical**

We don't always use Roaring Bitmap because Bloom filters are significantly more memory efficient. Bloom filters don't store actual values, so they can represent very large sets using minimal memory, at the cost of allowing false positives. In many systems, false positives are acceptable because we can do a second-level exact check using another structure or database. Roaring Bitmap is used when exact membership is required, but it uses more memory. So the choice depends on whether memory efficiency or exact correctness is more important.