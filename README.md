# Retry service provides policy for how often some operation should happen with the timeout limit and delay between events

The service creates sequence of the delays (nanoseconds) according to chosen strategy 

[![](https://img.shields.io/endpoint?url=https%3A%2F%2Fswiftpackageindex.com%2Fapi%2Fpackages%2Fswiftuiux%2Fretry-policy-service%2Fbadge%3Ftype%3Dplatforms)](https://swiftpackageindex.com/swiftuiux/retry-policy-service)

There are two strategies

| type | description |
| --- | --- |
| constant | The strategy implements constant backoff  |
| exponential [default] | Exponential backoff is a strategy in which you increase the delays between retries  |

```swift
        /// Constant delay between retries
        case constant(
            retry : UInt = 5,
            duration: DispatchTimeInterval,
            timeout: DispatchTimeInterval 
        )
        
        /// Exponential backoff is a strategy in which you increase the delays between retries
        case exponential(
            retry : UInt = 3,
            multiplier: Double = 2.0, // power exponent
            duration: DispatchTimeInterval,
            timeout: DispatchTimeInterval 
        )

```

## SwiftUI example

[example for retry service](https://github.com/swiftuiux/retry-policy-service-example)

## Packages using the package

[Async http client](https://github.com/swiftuiux/async-http-client)

## How to use

```swift
final class ViewModel : ObservableObject{
    
    func constant() async {
        let policy = RetryService(strategy: .constant())
        for delay in policy{
            try? await Task.sleep(nanoseconds: delay)
            // do something
        }
    }
    
    func exponential() async {
        let policy = RetryService(
                strategy: .exponential(
                retry: 5, 
                multiplier: 2, 
                duration: .seconds(1), 
                timeout: .seconds(5)
               )
             )
                
        for delay in policy{
            try? await Task.sleep(nanoseconds: delay)
            // do something
        }
    }
}

struct ContentView: View {
    
    @StateObject var model = ViewModel()
    
    var body: some View {
        VStack {
            Button("constatnt") { Task { await model.constant() } }
            Button("exponential") { Task { await model.exponential() } }
        }
        .padding()
        .task {
            await model.exponential()
        }
        
    }
}
```

## TODO:

Exponential backoff with jitter. Jitter adds some amount of randomness to the backoff to spread the retries around in time.
For more information [Exponential Backoff And Jitter](https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)

