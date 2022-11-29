**CoinGate SDK** provides a convenient access to the CoinGate API from
applications written in a specific programming language.

CoinGate API documentation -
[[https://developer.coingate.com/reference/cryptocurrency-payment-api]](https://developer.coingate.com/reference/cryptocurrency-payment-api)

CoinGate Sandbox -
[[https://sandbox.coingate.com]](https://sandbox.coingate.com)

Currently following languages are supported:

[[PHP]](https://github.com/coingate/coingate-php)

[[NodeJs]](https://github.com/coingate/coingate-node)

[[Python]](https://github.com/coingate/coingate-python)

**CoinGate SDK must has these API Endpoints**

-   Auth test:
    [[https://developer.coingate.com/reference/api-authentication]](https://developer.coingate.com/reference/api-authentication)

-   Orders

    -   Create Order:
        [[https://developer.coingate.com/reference/create-order]](https://developer.coingate.com/reference/create-order)

    -   Get Order:
        [[https://developer.coingate.com/reference/get-order]](https://developer.coingate.com/reference/get-order)

    -   List Orders:
        [[https://developer.coingate.com/reference/list-orders]](https://developer.coingate.com/reference/list-orders)

    -   Checkout Order:
        [[https://developer.coingate.com/reference/checkout]](https://developer.coingate.com/reference/checkout)

-   Merchant refunds

    -   Create Order Refund
        [[https://developer.coingate.com/reference/create-refund]](https://developer.coingate.com/reference/create-refund)

    -   Get Order Refund
        [[https://developer.coingate.com/reference/get-order-refund]](https://developer.coingate.com/reference/get-order-refund)

    -   Get Order Refunds
        [[https://developer.coingate.com/reference/get-refund]](https://developer.coingate.com/reference/get-refund)

    -   Get Refunds
        [[https://developer.coingate.com/reference/get-refunds]](https://developer.coingate.com/reference/get-refunds)

-   Public

    -   Get Exchange Rate
        [[https://developer.coingate.com/reference/get-rate]](https://developer.coingate.com/reference/get-rate)

    -   List Exchange Rates
        [[https://developer.coingate.com/reference/list-rates]](https://developer.coingate.com/reference/list-rates)

    -   Ping
        [[https://developer.coingate.com/reference/ping]](https://developer.coingate.com/reference/ping)

    -   IP Addresses
        [[https://developer.coingate.com/reference/ip-addresses]](https://developer.coingate.com/reference/ip-addresses)

    -   Get Currencies
        [[https://developer.coingate.com/reference/currencies]](https://developer.coingate.com/reference/currencies)

    -   Platforms
        [[https://developer.coingate.com/reference/platforms]](https://developer.coingate.com/reference/platforms)

**CoinGate SDK requirements**

* Possibility to set both API Token and environment via constructor, or define via methods later.

    ```php
    //PHP SDK example
    $client = new \CoinGate\Client('YOUR_API_TOKEN');
    //PHP SDK example (sandbox)
    $client = new \CoinGate\Client('YOUR_API_TOKEN', true);
    //PHP SDK example (late definition)
    $client = new CoinGate\Client(); 
    // if needed you can set configuration parameters later
    $client->setApiKey('YOUR_API_TOKEN'); 
    $client->setEnvironment('sandbox');
    ```

-   The following endpoints MUST BE implemented ([[see CoinGate API documentation]](https://developer.coingate.com/reference/api-overview))

    

    -   Create Order

        ```php
        //PHP SDK example
        $params = [...];
        $order = $client->order->create($params);
        ```

    -   Checkout

        ```php
        //PHP SDK example
        $checkout = $client->order->checkout(7294, [ 'pay_currency' => 'BTC' ])
        ```

    -   Get Order

        ```php
        //PHP SDK example
        $order = $client->order->get(7294);
        ```

    -   List Orders

        ```php
        //PHP SDK example
        $orders = $client->order->list([ 'created_at' => [ 'from' => '2022-01-25' ] ]);
        ```

-   Possibility to perform Test API Connection

    ```php
    //PHP SDK example
    \CoinGate\Client::testConnection('YOUR_API_TOKEN');
    //PHP SDK example (sandbox)
    \CoinGate\Client::testConnection('YOUR_API_TOKEN', true);
    ```

-   Possibility to set App Information (used for 3rd party plugin developers)

    ```php
    //PHP SDK example
    \CoinGate\Client::setAppInfo("MyAwesomePlugin", "1.0.0");

    ```

-   Possibility to set Custom Request Timeout

    ```php
    //PHP SDK example
    $curl = new \CoinGate\HttpClient\CurlClient(); 
    $curl->setTimeout(10); 
    $curl->setConnectTimeout(5); 
    // tell CoinGate Library to use the tweaked Curl client 
    \CoinGate\Client::setHttpClient($curl); 
    // use the CoinGate API client as you normally would
    ```


-   Every request to the API endpoint should include a User-Agent header in the following format:

    > CoinGate/v2 (**%language%** Library v**%version%**)
    >
    > *where*
    >
    > **%language%** is programming language used e.g. *PHP, JAVA, C#,
    > etc..*
    >
    > **%version%** is version of the SDK library, e.g. *1.0.0*
    >
    > In case, SDK will be integrated into a 3rd party package, there should
    > be a possibility to set an additional app information, e.g.
    >
    > CoinGate/v2 (**%language%** Library v**%version%**, **%appInfoName%**
    > v**%appInfoVersion%**)
    >
    > CoinGate/v2 (**%language%** Library v**%version%**, **%appInfoName%**)
    >
    > *where*
    >
    > **%appInfoName%** is name of 3rd party package e.g. *WHMCS, ShopWare6,
    > etc..*
    >
    > **%appInfoVersion%** is version of the 3rd party package (optional),
    > e.g. *1.0.0*

-   SDK usage SHOULD BE replicated as close as to one used in CoinGate PHP SDK [[https://github.com/coingate/coingate-php]](https://github.com/coingate/coingate-php)

-   SDK source code SHOULD BE covered with tests. Tests should be mocked using fake possible request responses, so tests can be passed without real api-key from live or sandbox.

-   Programming language specific code style and best practices MUST BE followed.

-   SDK SHOULD handle both Socket and API response exceptions via a central method

    **PHP SDK snippet for Socket exception cases and messages**

    ```php
    private function handleCurlError(string $url, int $errno, string $message)
    {
        switch ($errno) {
            case CURLE_COULDNT_CONNECT:
            case CURLE_COULDNT_RESOLVE_HOST:
            case CURLE_OPERATION_TIMEOUTED:
                $response = "Could not connect to CoinGate ($url). "
                . "Please check your internet connection and try again. ";

                break;

            case CURLE_SSL_CACERT:
            case CURLE_SSL_PEER_CERTIFICATE:
                $response = "Could not verify CoinGate's SSL certificate. "
                . "Please make sure that your network is not intercepting certificates. "
                . "(Try going to $url in your browser.) ";

                break;

            default:
                $response = "Unexpected error communicating with CoinGate. ";
        }

        $response .= 'If this problem persists, let us know at https://support.coingate.com.';
        $response .= "\n\n(Network error [errno $errno]: $message)";

        throw new ApiConnectionException($response);
    }
    ```

    **PHP SDK snippet for API response exception cases and messages**

    ```php
    public function handleErrorResponse($response, int $httpStatus)
    {
        $reason = $response['reason'] ?? null;

        if ($httpStatus === 400) {
            throw BadRequest::factory($response, $httpStatus);
        } elseif ($httpStatus === 401) {
            switch ($reason) {
                case 'BadAuthToken':
                    throw BadAuthToken::factory($response, $httpStatus);

                default:
                    throw Unauthorized::factory($response, $httpStatus);
            }
        } elseif ($httpStatus === 404) {
            switch ($reason) {
                case 'RefundNotFound':
                    throw RefundNotFound::factory($response, $httpStatus);

                case 'LedgerAccountNotFound':
                    throw LedgerAccountNotFound::factory($response, $httpStatus);

                case 'OrderNotFound':
                    throw OrderNotFound::factory($response, $httpStatus);

                case 'WithdrawalNotFound':
                    throw WithdrawalNotFound::factory($response, $httpStatus);

                default:
                    throw NotFound::factory($response, $httpStatus);
            }
        } elseif ($httpStatus === 422) {
            switch ($reason) {
                case 'RefundIsNotValid':
                    throw RefundIsNotValid::factory($response, $httpStatus);

                case 'OrderIsNotValid':
                    throw OrderIsNotValid::factory($response, $httpStatus);

                case 'OrderNotFound':
                    throw OrderNotFound::factory($response, $httpStatus);

                default:
                    throw UnprocessableEntity::factory($response, $httpStatus);
            }
        } elseif ($httpStatus === 429) {
            throw RateLimitException::factory($response, $httpStatus);
        } elseif (in_array($httpStatus, [500, 504])) {
            throw InternalServerError::factory($response, $httpStatus);
        }

        throw UnknownApiErrorException::factory($response, $httpStatus);
    }
```
