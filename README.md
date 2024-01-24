# Define variables
log_group_name="your-log-group"
start_time=$(date --date='4 months ago' +%s)
end_time=$(date +%s)
s3_bucket="your-s3-bucket"
output_file="cloudwatch_logs.json"

# Custom query string
query_string='fields @timestamp, @message | filter log = "dbs" | sort @timestamp desc | limit 10000'

# Echo the query string for debugging
echo "Query String: $query_string"

# Start the CloudWatch Logs query
query_response=$(aws logs start-query \
  --log-group-name "$log_group_name" \
  --start-time $((start_time * 1000)) \
  --end-time $((end_time * 1000)) \
  --query-string "$query_string")

# Extract the query ID from the response
query_id=$(echo $query_response | jq -r '.queryId')

# Echo the query ID for debugging
echo "Query ID: $query_id"

# Wait for the query to complete and get the results
echo "Waiting for query to complete..."
sleep 10 # Adjust sleep time as needed

while [[ "$(aws logs get-query-results --query-id "$query_id" --output text --query 'status')" == "Running" ]]; do
  echo "Query still running..."
  sleep 5
done

aws logs get-query-results --query-id $query_id > $output_file

# Copy the file to S3
aws s3 cp $output_file s3://$s3_bucket/$output_file
