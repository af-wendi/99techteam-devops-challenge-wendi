Provide your CLI command here:
# First i run this command give the file to output1.txt, but this run just using example.com it's not looks like good (just redirect page)
grep 'TSLA' ./transaction-log.txt | grep '"side": "sell"' | jq -r '.order_id' | xargs -I {} curl -s https://example.com/api/{} >> ./output1.txt

# Second I test using https://httpbin.org/get?order_id to get order_id and to output2.txt, but it's not see the TSLA
grep 'TSLA' ./transaction-log.txt | grep '"side": "sell"' | jq -r '.order_id' | xargs -I {} curl -s https://httpbin.org/get?order_id={} >> ./output2.txt

# And the third testing im using more solution that see the TSLA in output3.txt
grep 'TSLA' ./transaction-log.txt | grep '"side": "sell"' | jq -r '.order_id + " " + .symbol + " " + (.quantity | tostring) + " " + (.price | tostring)' | while read order_id; do curl -s https://httpbin.org/get?order_id=${order_id%% *} | jq --arg details "$order_id" '. + {details: $details}' >> ./output3.txt; done