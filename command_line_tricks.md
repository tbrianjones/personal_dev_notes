Command Line Tricks
===================

Data Processing
---------------

### Create file with random rows for sampling
- `shuf -n 1000 input_file_name > output_file_name`
- A 500M row input_file took 13 minutes. The rows were small though. Doubt that affects it. Should be a minute or two on 50M record file?!?
- shuf is faster than sort -R: http://stackoverflow.com/questions/9245638/select-random-lines-from-a-file-in-bash

### Go To Specific Lines with `less`
- `less +100g bigfile.txt`
  - 100g to go to the 100th line
  - 50p to go 50% into the file
  - 100P to go to the line containing 100th byte
- more cool options: http://stackoverflow.com/questions/8586648/going-to-a-specific-line-number-using-less-in-unix

### Merge Text Files with Headers
- Write header to `final_file`
  - `head -n 1 file_with_header > final_file`
- Append data from other files (skipping the header in them)
  - add a new line if you have to: `echo "" >> final_file` (you have to test this and can't rely on new line characters in editors)
  - `tail -n +2 file_to_append >> final_file`
- Notes:
  - `head -n 1` grabs the first line of a file
  - `tail -n +2` grabs the second line and every line after it
  - `>` overwrites the final_file and `>>` appends to it
- Process Times
  - it took 10min to write a 30GB file to a final_file on EC2/EBS
  - this file had 65M rows and was a csv with ~150 sparsly populates columns

### Delete all lines after match (including matched line)
- `sed -n '/text to match. pipes are ok in here./q;p' file_to_match_in > file_to_write_to`
- this rewrites the entire file to a new file (requiring the full file size in disk space)

### Remove new line from end of file
- `perl -pi -e 'chomp if eof' file_to_remove_new_line_from`
- this rewrites the entire file in place (overwrites existing file, and uses full file of disk space)
- SO: http://stackoverflow.com/questions/1654021/how-can-i-delete-a-newline-if-it-is-the-last-character-in-a-file
