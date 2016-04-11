# Intro 
 This repo is to reproduce an issue experienced in the ruby client of riak ts.
 Timestamp values seem to be parsed for serialization incorrectly even though the data is correct.
 
# Setup
```
git clone git@github.com:davidx/tsdateissue.git
cd tsdateissue
bundle 
```
# Create schema
```
bundle exec rake create_schema
```
# Load data 
```
bundle exec rake load_data
```
# Read data
```
bundle exec rake read_data
```
# riak-shell
Run the query on the last line of read_data output.
```
riak-shell
```
