
# go.sns
A helper library for receiving [Amazon AWS SNS](https://aws.amazon.com/sns/) [HTTP(S) notifications](https://docs.aws.amazon.com/sns/latest/dg/SendMessageToHttp.html).

It provides [signature validation](https://docs.aws.amazon.com/sns/latest/dg/SendMessageToHttp.verify.signature.html) for payloads and conveinence functions for subscribing and unsubscribing from topics.

## Usage

### Verifying a HTTP POST (a payload)

```go
import (
  "encoding/json"
  "fmt"

  "github.com/cwxstat/go.sns"
)

var notificationPayload sns.Payload
err := json.Unmarshal([]byte(notificationJson), &notificationPayload)
if err != nil {
  fmt.Print(err)
}
verifyErr := notificationPayload.VerifyPayload()
if verifyErr != nil {
  fmt.Print(verifyErr)
}
fmt.Print("Payload is valid!")
```

## Example with Labstack Echo

```go
// Handler
func aws(c echo.Context) error {
	log.Println("aws called...")


	var notificationPayload sns.Payload
	err := json.NewDecoder(c.Request().Body).Decode(&notificationPayload)

	if err != nil {
		log.Print(err)
	}
	verifyErr := notificationPayload.VerifyPayload()
	if verifyErr != nil {
		log.Print(verifyErr)
    return c.String(http.StatusBadRequest, "")
	}

	log.Print("Payload is valid!")

	return c.String(http.StatusOK, "")
}

```



### Subscribing to a topic

```go
import (
  "encoding/json"
  "fmt"

  "github.com/robbiet480/go.sns"
)

// If it's a SubscriptionConfirmation or UnsubscribeConfirmation
subscriptionResponse, err := notificationPayload.Subscribe()
if err != nil {
  fmt.Println("Error when subscribing!", err)
}
fmt.Printf("subscriptionResponse %+v", subscriptionResponse)
```

### Unsubscribing from a topic

```go
import (
  "encoding/json"
  "fmt"

  "github.com/robbiet480/go.sns"
)

// If it's a Notification
unsubscriptionResponse, err := notificationPayload.Unsubscribe()
if err != nil {
  fmt.Println("Error when unsubscribing!", err)
}
fmt.Printf("unsubscriptionResponse %+v", unsubscriptionResponse)
```

## Thanks
This library was based off work by [lazywei](https://github.com/lazywei), found on [this Stack Overflow question](http://stackoverflow.com/q/20014908/486182) and code written by [syama666](https://github.com/syama666).

Thanks also goes to [xibz](https://github.com/xibz) for helping me work out some of the low level certificate/SHA1WithRSA stuff in this [issue on aws-sdk-go](https://github.com/aws/aws-sdk-go/issues/567).

## Contributing
Fork, edit, write & run tests, submit PR, success!

## Tests
Tests are written but not passing because the payload string is an example from the documentation.

## License
MIT
