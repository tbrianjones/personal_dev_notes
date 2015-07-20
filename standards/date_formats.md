Date Formats
============


try to use the ISO 8601 format
------------------------------
*http://en.wikipedia.org/wiki/ISO_8601*
- YYYY-MM-DDThh:mm:ss+hh:mm ( eg. 2004-02-12T15:19:21+07:00 )
	- specifics for representing years, months, dates in our systems:
	- if you are just representing a date, set the time to midnight and greenwich mean time zone ( eg. YYYY-MM-DDT00:00:00+00:00 )
	- if you're just representing a month, set the day to the 1st ( eg. YYYY-MM-01T00:00:00+00:00 )
	- if you're just representing a year, set the month and day to january and the 1st ( eg. YYYY-01-01T00:00:00+00:00 )

- data going into any of our production systems needs to conform to the ISO 8601 date standard format
	- this includes all database and search engines
	- data pulled from third party systems does not need to be stored as iso 8601 as long as it isn't being accessed by our api in production
- our api will return dates in the iso 8601 format no matter which system it pulls from
	- the api should not have to convert date types to iso 8601


this format in specific languages
---------------------------------

### PHP
*http://php.net/manual/en/function.date.php*
- `date( 'c' )`

### Python
*http://stackoverflow.com/questions/2150739/iso-time-iso-8601-in-python*
- `from time import strftime`
- `strftime( "%Y-%m-%d %H:%M:%S" )`

### shell

