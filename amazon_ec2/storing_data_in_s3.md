Storing Data in S3
===================


notes
-----
- whenever possible, store the files / data as the id that identifies them in our database, followed by the extension you want to acces them by.
	- eg. in dla-solicitation-rfqs-profiles, we store files as id.x12, and id.json.  the id comes from our edi_downloader database which tracks files we have downloaded and processed.  we store the original x12 file and eventually will store a json representation of the solicitaitons as well.
- add the correct content-type to files stored on s3.
	- eg. x12 files stores in dla-solicitation-rfqs-profiles are assigned the content type of `application/EDI-X12`
	

s3 mac client
-------------
*this client struggles when a folder contains hundreds of thousands of files*
- takes 20 min to load all the files within a bucket that contains hundreds of thousands of files