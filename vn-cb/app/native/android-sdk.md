# Sovendus Android SDK

[![GitLab CI](https://img.shields.io/gitlab/pipeline-status/sdk/sovendus-sdk-android?branch=main&gitlab_url=https%3A%2F%2Fgitlab.sovendus.com)](https://gitlab.sovendus.com/sdk/sovendus-sdk-android/-/pipelines)
[![Maven Central](https://img.shields.io/maven-central/v/com.sovendus/android-sdk)](https://central.sonatype.com/artifact/com.sovendus/android-sdk)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

Android SDK for Sovendus vouchers and product recommendations.

## Features

- **Simple Integration** - One component that automatically renders content from API
- **Auto-Initialization** - No setup required
- **Personalization** - Consumer and order data support
- **Material 3** - Automatic theming

## Installation

```kotlin
dependencies {
    implementation("com.sovendus:android-sdk:1.1.0")
}
```

## Quick Start

### Inline Display
```kotlin
// 1. Create configuration
val config = SovendusConfig.Builder()
    .trafficSourceNumber(123)
    .trafficMediumNumber(456)
    .build()

// 2. Add component (Compose)
SovendusSDK.VoucherBenefits(config = config)
```

That's it! The component automatically displays whatever content the API returns.

### Multiple Placements

Use `VoucherBenefits` for all placements—inline, dialogs, modals, etc. Each placement uses a different `trafficMediumNumber` assigned by Sovendus:

```kotlin
// Placement 1: Inline on checkout success
SovendusSDK.VoucherBenefits(
    config = SovendusConfig.Builder()
        .trafficSourceNumber(123)
        .trafficMediumNumber(456)  // Inline placement
        .build()
)

// Placement 2: Modal/dialog (different TMN)
SovendusSDK.VoucherBenefits(
    config = SovendusConfig.Builder()
        .trafficSourceNumber(123)
        .trafficMediumNumber(789)  // Modal placement
        .build(),
    onStateChanged = { if (it is LoadingState.Success) showDialog = true }
)
```

Sovendus configures what content/layout each medium number returns via the backend.

## Configuration

### Basic

```kotlin
val config = SovendusConfig.Builder()
    .trafficSourceNumber(123)      // Required
    .trafficMediumNumber(456)      // Required
    .build()
```

### With Personalization
```kotlin
val config = SovendusConfig.Builder()
    .trafficSourceNumber(123)
    .trafficMediumNumber(456)
    .consumer(ConsumerData(
        firstName = "Max",
        email = "max@example.com",
        zipCode = "12345"
    ))
    .order(OrderData(
        orderId = "ORDER-123",
        orderValue = 99.99,
        orderCurrency = "EUR"
    ))
    .build()
```

### With Custom Link Handler

```kotlin
val config = SovendusConfig.Builder()
    .trafficSourceNumber(123)
    .trafficMediumNumber(456)
    .onLinkOpened { url ->
        openInCustomTabs(url)
        true
    }
    .build()
```

## Loading States

```kotlin
SovendusSDK.VoucherBenefits(
    config = config,
    onStateChanged = { state ->
        when (state) {
            is LoadingState.Loading -> { /* handle loading */ }
            is LoadingState.Success -> { /* handle success */ }
            is LoadingState.Error -> { /* handle error */ }
        }
    }
)
```

## XML Integration

```xml
<com.sovendus.sdk.ui.xml.SovendusVoucherBenefitsView
    android:id="@+id/sovendus_benefits"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

```kotlin
findViewById<SovendusVoucherBenefitsView>(R.id.sovendus_benefits).apply {
    config = myConfig
}
```

## Testing

Use sandbox mode for testing:

```kotlin
val testConfig = SovendusConfig.Builder()
    .sandbox(true)  // Uses test data
    .build()
```

## Documentation

- **[Integration Guide](INTEGRATION_GUIDE.md)** - Detailed integration steps
- **[Sample App](sample/README.md)** - Working examples

## Requirements

- Android API 24+ (Android 7.0+)
- Kotlin 2.1.0+
- Jetpack Compose BOM 2025.08.01+ (for Compose)

## License

Apache License 2.0 - See [LICENSE](LICENSE) file

---
**Need help?** Contact your Sovendus integration specialist