

require 'json'
require 'riak'
require 'yaml'

schema=IO.read(File.dirname(__FILE__) + '/db/schema.txt').gsub(/\n/,'')

SCHEMA = { "props" => { "table_def" => "#{schema}"}}
RIAK_HOST=ENV['RIAK_HOST']||"localhost"
PB_PORT=ENV['PB_PORT']||8087
TABLE_NAME="market_data"
CLIENT = Riak::Client.new( :host => RIAK_HOST, :pb_port => PB_PORT)

task :create_schema do
  system "riak-admin bucket-type create #{TABLE_NAME} \'#{SCHEMA.to_json}\'"
  system "riak-admin bucket-type activate #{TABLE_NAME}"
end


task :load_data do
 # <ticker>,<date>,<open>,<high>,<low>,<close>,<vol>
  market='NASDAQ'
  measurements=[]
  lines=IO.readlines(File.dirname(__FILE__) + '/db/NASDAQ_AAPL.txt')
  lines.shift

  lines.each do |line|

    ticker,date,open,high,low,close,vol = line.strip.split(/,/)
    date=DateTime.parse(date)
    p "date:"
    p date

    time = date.strftime("%s").to_i
    p time
    measurements.push([ market, ticker, time.to_i, open.to_f, high.to_f, low.to_f, close.to_f, vol.to_i ])
  end
  submission = Riak::TimeSeries::Submission.new CLIENT, TABLE_NAME
  submission.measurements = measurements
  print measurements.to_yaml
  submission.write!
end

task :read_data do
  query=<<-DOC
   SELECT * FROM #{TABLE_NAME}
   WHERE time > 1286787600  AND
         time < 1289235000 AND
         market='NASDAQ' AND
         ticker='AAPL'
  DOC
  query.gsub!(/\n/,'')
  results = Riak::TimeSeries::Query.new(CLIENT, query).issue!

  print results.to_yaml
  print "\n\nDEBUG\n\n"
  print "See above time values are all set like: '1970-01-15 14:07:03.600000000 -08:00' or in epoch:  \n"
  print "=> \"1260423\"" 
  print "\nTry running in riak-shell to see correct time values: #{query}\n\n" 
end

