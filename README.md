# hakaru

Apple Watch × React Native heart rate measurement and analysis app. Measure and analyze your pulse during walking workouts.

## Overview

**hakaru** (測る) is a heart rate monitoring app designed to work seamlessly between Apple Watch and iPhone. The primary goal is to enable interval training based on heart rate zones, with the Apple Watch handling live measurements and the iPhone providing detailed analysis and history visualization.

### Current Status: v0 - Foundation

We're starting with the minimum viable implementation:
- Apple Watch: Walking workout sessions with live heart rate display
- iPhone: Historical data analysis with 1-minute aggregations
- Focus: Prove the core value before expanding features

## Architecture

### Target Platforms
- **iOS**: 17+ (React Native + react-native-health)
- **watchOS**: 10+ (Swift + SwiftUI + HealthKit)
- **Test Device**: Apple Watch Ultra (latest OS)

### Responsibilities

#### Apple Watch (Swift/SwiftUI)
- HKWorkoutSession for walking workouts
- Live heart rate display with EMA smoothing (α=0.3) + 3-sample median filtering
- Auto-stop conditions: 60min max, 3min no-movement, 2min rest-heart recovery
- Local notifications for safety confirmations

#### iPhone (React Native)
- Historical data retrieval via react-native-health
- 1-minute bucket aggregation (avg/max/95th percentile)
- Simple line charts for trend visualization
- Settings for thresholds (future)

### Data Flow
1. **Measurement**: Watch collects HR samples → HealthKit storage
2. **Sync**: Data appears on iPhone through HealthKit (depends on user's iCloud Health sync settings)
3. **Analysis**: iPhone aggregates and visualizes historical trends

## Technical Specifications

### Heart Rate Display Smoothing
- **EMA (Exponential Moving Average)**: α=0.3 for balance of responsiveness and stability
- **Outlier Suppression**: 3-sample median filter before EMA application
- **Update Frequency**: 1Hz display updates during workouts

### Auto-Stop Safety Features
1. **Maximum Duration**: 60 minutes → notification → confirmation → stop
2. **No Movement**: 3 minutes of minimal step/pace change → notification
3. **Rest Recovery**: 2 minutes below (estimated rest HR + 10bpm) → notification

### Data Aggregation
- **Live**: Smoothed display on Watch
- **Historical**: 1-minute buckets with avg/max/p95 metrics
- **Storage**: HealthKit (user controls iCloud sync)

## Development Setup

### Prerequisites
- Xcode with iOS 17+ / watchOS 10+ SDKs
- Node.js and React Native development environment
- Apple Watch Ultra paired with iPhone for testing

### Project Structure
```
hakaru/
├── ios/                    # iOS app (React Native)
│   ├── Capabilities: HealthKit enabled
│   └── Info.plist: Health permissions
├── watchOS/               # Apple Watch app (Swift)
│   ├── WorkoutManager.swift
│   └── ContentView.swift
├── src/                   # React Native TypeScript
│   ├── health.ts         # HealthKit bridge
│   └── components/       # UI components
└── docs/
    ├── SPEC.md           # Detailed specifications
    └── DEPLOYMENT.md     # Build and release process
```

### Installation

1. **Clone and install dependencies**
   ```bash
   git clone https://github.com/MAA39/hakaru.git
   cd hakaru
   npm install
   npx pod-install ios
   ```

2. **Enable HealthKit in Xcode**
   - iOS target: Signing & Capabilities → HealthKit
   - watchOS target: Signing & Capabilities → HealthKit
   - Add required Info.plist permissions (see SPEC.md)

3. **Build and test**
   ```bash
   npx react-native run-ios
   # Open watchOS target in Xcode for Watch app
   ```

## Privacy & Permissions

### HealthKit Permissions
The app requires heart rate read access. Users can manage permissions in:
- iPhone: Settings → Privacy & Security → Health → Data Access & Devices
- Health app: Profile → Data Access & Devices

### Data Sync
- **Local**: All data stays in device HealthKit by default
- **iCloud**: Only syncs between user's devices if they enable Health iCloud sync
- **No External Servers**: No data leaves Apple's ecosystem

### Info.plist Templates
```xml
<key>NSHealthShareUsageDescription</key>
<string>このアプリは運動中の心拍データを読み取り、履歴の可視化に利用します。iPhoneの「ヘルスケア」→「データアクセスとデバイス」から許可を管理できます。</string>
<key>NSHealthUpdateUsageDescription</key>
<string>（将来的に）ワークアウトの保存や指標の記録にヘルスデータを書き込む場合があります。</string>
```

## Project Roadmap

### v0: Foundation (Current)
- [ ] React Native + react-native-health integration
- [ ] Basic HealthKit permissions and data retrieval
- [ ] Apple Watch walking workout sessions
- [ ] Live HR display with EMA smoothing
- [ ] Auto-stop safety features
- [ ] 1-minute historical aggregation and simple charts

### v1: Enhanced Analysis
- [ ] Heart rate zone definitions and zone-based training
- [ ] Interval workout templates
- [ ] Enhanced data export capabilities
- [ ] Customizable auto-stop thresholds

### v2: Advanced Features
- [ ] WatchConnectivity for real-time iPhone display
- [ ] Independent Watch app (iPhone-optional mode)
- [ ] Advanced analytics and training insights
- [ ] Subscription features for premium analysis

## Contributing

This project follows a specification-driven development approach:

1. **Specifications**: Major features start as specification issues for alignment
2. **Tasks**: Implementation work is broken into 1-2 hour tasks
3. **Subtasks**: Tasks are divided into ≤30 minute subtasks for steady progress

See GitHub Issues for current development status and contribution opportunities.

## License

TBD

## Support

For technical issues or feature requests, please use GitHub Issues.

---

**Note**: This app is designed for fitness enthusiasts who want precise heart rate monitoring during workouts. It's not intended as a medical device or for clinical diagnosis.
