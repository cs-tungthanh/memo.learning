# Requirement
## Input
- A file with 10 million rows of user in CSV Format
	- ID: string, amount: int
- Design a scalable and reliable system to support accountants transfer money to all recipients listed in a CSV file
	- how can the system be scaled?


## Functional requirement
- Support to upload CSV file containing 10 million rows
- Process transfer money following by info each row in file (userId and amount)
- Monitor
	- Generate report status of transaction process
	- Retry for failed transaction
## Non-functional requirement
- Performance: process transaction in minimal time
- Scalability: handle increasing transaction volume efficiently, file may larger
- Reliability: ensure transaction are completed without duplication or omission
- Monitor: tracking transaction failure and system heath
- Security: protect sensitive payment data


