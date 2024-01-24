# Define variables
log_group_name="your-log-group"
start_time=$(date --date='4 months ago' +%s)
end_time=$(date +%s)
s3_bucket="your-s3-bucket"
output_json="cloudwatch_logs.json"
output_csv="cloudwatch_logs.csv"

# Start the CloudWatch Logs query
query_id=$(aws logs start-query --log-group-name "$log_group_name" \
  --start-time $((start_time * 1000)) \
  --end-time $((end_time * 1000)) \
  --query-string "fields @timestamp, @message | sort @timestamp desc | limit 10000" \
  --output text --query 'queryId')

# Wait for the query to complete and get the results
sleep 10 # Adjust sleep time as needed
aws logs get-query-results --query-id $query_id > $output_json

# Convert JSON to CSV
jq -r '.results[] | map(.value) | @csv' $output_json > $output_csv

# Copy the CSV file to S3
aws s3 cp $output_csv s3://$s3_bucket/$output_csv
