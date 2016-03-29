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
