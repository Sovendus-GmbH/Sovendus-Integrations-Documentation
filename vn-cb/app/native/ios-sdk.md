# Sovendus iOS SDK

[![Swift Package Manager](https://img.shields.io/badge/Swift%20Package%20Manager-compatible-brightgreen.svg)](https://swift.org/package-manager/)

Production-ready iOS SDK for Sovendus integration with customer benefits and vouchers.

## Features

- **Native SwiftUI Components** - Modern UI components with smooth animations and native iOS design
- **Flexible Integration** - SwiftUI primary support with UIKit compatibility for existing apps
- **Production Ready** - Comprehensive testing, error handling, and Swift Package Manager distribution
- **Customer Personalization** - Dynamic content based on customer data
- **Automatic Theming** - Pre-built themes applied based on traffic source configuration, including gradient backgrounds (linear, radial, angular)
- **Easy Integration** - Simple configuration with minimal setup required

## Quick Start

### Installation

#### Swift Package Manager

Add to your Xcode project:

1. Go to **File > Add Package Dependencies**
2. Enter: `https://github.com/Sovendus-GmbH/sovendus-sdk-ios`
3. Select version and add to target

### Basic Usage

```swift
import SovendusSDK

// Production configuration (default mode - traffic numbers required)
let config = SovendusConfig(
    trafficSourceNumber: 123,
    trafficMediumNumber: 456,
    consumerData: customerData,
    orderData: orderData,
    sandbox: false, // Production mode
    enableDebugLogs: false,  // Disable debug logging
    onLinkOpened: { url in
        // Optional: Handle link opening yourself
        // If not provided, uses UIApplication.shared.open(url)
        print("Opening link: \(url)")
        UIApplication.shared.open(url)
    }
)

// Testing configuration (sandbox mode - uses test data)
let testConfig = SovendusConfig(
    trafficSourceNumber: 123,
    trafficMediumNumber: 456,
    consumerData: nil,
    orderData: nil,
    sandbox: true, // For testing - uses sandbox data
    enableDebugLogs: true  // Enable debug logging
)

// Use VoucherBenefitsView component
VoucherBenefitsView(
    config: config,
    onStateChanged: { state in
        // Handle state changes
    }
)
```

See the [Integration Guide](INTEGRATION_GUIDE.md) for detailed integration examples.

## Development Setup

### Prerequisites

- Xcode 15.0 or later
- iOS 16.0+
- Swift 5.7+
- macOS 12.0+ (for development)

### Clone & Build Sample App

```bash
# Clone Repository
$ git clone https://gitlab.sovendus.com/sdk/sovendus-sdk-ios
$ cd sovendus-sdk-ios/SampleApp

# Checkout Git Submodules
$ git submodule update --init --recursive

# Open Xcode Project
$ open SovendusSdkSample.xcodeproj
```

## Publishing

### Building the XCFramework

```bash
# 1. Install SPM plugin for creating multi architecture XCFramework
$ brew install segment-integrations/formulae/swift-create-xcframework

# 2. Build the actual XCFramework
$ ./build-xcframework.sh

# 3. XCFramework will be outputted to ./Output
```

## Project Structure

```
sovendus-sdk-ios/
├── SovendusSDK/
│   ├── Sources/
│   │   └── Sovendus/           # Main SDK library
│   │       ├── UI/
│   │       │   └── Components/ # SwiftUI components
│   │       ├── Network/        # API client
│   │       ├── Models/         # Data models
│   │       └── Theming/        # Theme system
│   ├── Tests/                  # Unit tests
    └── Package.swift           # Swift Package Manager manifest
├── SampleApp/                  # Sample integration app
```

## Documentation

- **[Integration Guide](INTEGRATION_GUIDE.md)** - Complete integration setup and examples
- **[GitHub Actions Setup](GITHUB_ACTIONS.md)** - CI/CD pipeline configuration
- **[Publishing Guide](PUBLISHING.md)** - Distribution setup for SPM and CocoaPods
- **[Sample App](SampleApp/README.md)** - Integration examples and theming

## Architecture

### Core Components

- **VoucherBenefitsView** - API driven component that renders whatever the backend returns: banner, product list, or both. Supports multiple layout modes (inline/card banner, slider/vertical list) controlled via backend configuration.

### Automatic Theming

The SDK automatically applies appropriate themes based on your configuration:

```swift
let config = SovendusConfig(
    trafficSourceNumber: 1234,
    trafficMediumNumber: 5678,
    customerData: customerData,
    orderData: orderData
)
```

- **Zero Configuration** - Themes applied automatically based on traffic source
- **Consistent Branding** - Each integration gets its dedicated visual style
- **Gradient Backgrounds** - Supports linear, radial, and angular gradients on containers, buttons, and badges
- **SwiftUI Native** - Works seamlessly with your app's existing design system

## Error Handling

The SDK implements different error handling behavior based on the configuration mode:

### Debug Mode (logging = true, sandbox = true)
- **Console Logging** - Detailed error information logged to Xcode console
- **Developer Feedback** - Clear indication when SDK operations fail
- **Network Error Details** - Comprehensive debugging information
- **Configuration Validation** - Warnings for missing or invalid parameters

### Production Mode (logging = false, sandbox = false)
- **Silent Failures** - SDK components render nothing when errors occur
- **No Console Spam** - Minimal logging to avoid performance impact
- **Graceful Degradation** - App continues to function normally
- **Error State Handling** - Components handle errors internally

Configure debug mode for development builds:

```swift
let config = SovendusConfig(
    trafficSourceNumber: 123,
    trafficMediumNumber: 456,
    consumerData: customerData,
    orderData: orderData,
    sandbox: true, // Enable sandbox mode for testing
    enableDebugLogs: true // Enable debug logging in development
)
```

**Important**: In production mode, consuming applications receive no notification when SDK operations fail. Monitor application logs for SovendusSDK-related messages to detect issues.

## Testing

```bash
# Run all tests
xcodebuild test -scheme SovendusSDK -destination 'platform=iOS Simulator,name=iPhone 15'

# Run tests with coverage
xcodebuild test -scheme SovendusSDK -destination 'platform=iOS Simulator,name=iPhone 15' -enableCodeCoverage YES
```

**Made with love by the Sovendus Team**