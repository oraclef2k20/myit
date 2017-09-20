```
aws dynamodb batch-write-item --request-items file://json
{
    "UnprocessedItems": {}
}
```

`Error parsing parameter '--request-items': Invalid JSON: Expecting property name enclosed in double quotes:`


lambdaの場合はの場合はダブルクオーテーションなくても大丈夫ですがコマンドラインの場合は厳格にチェックされるのでダブルクオーテーションで囲う事
