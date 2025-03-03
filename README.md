# Bitcoin Price Monitor Pro

![Python Version](https://img.shields.io/badge/python-3.7+-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![Last Updated](https://img.shields.io/badge/last%20updated-2025--03--03-brightgreen.svg)

A professional-grade Bitcoin price monitoring application with real-time price tracking, trend analysis, and price prediction capabilities. Built with Python and modern data science libraries.

![Application Screenshot](screenshots/app_screenshot.png)

## Features

- **Real-time Price Tracking**: Monitors Bitcoin price in real-time using multiple reliable cryptocurrency APIs
- **Price Prediction**: Implements polynomial regression to predict short-term price trends
- **Interactive Visualization**: Dynamic charts with real-time updates and trend analysis
- **Multi-source Data**: Fetches data from multiple sources (Binance, CryptoCompare, CoinGecko) for reliability
- **Professional UI**: Clean and intuitive user interface with key information display
- **Robust Error Handling**: Comprehensive error handling and logging system
- **Automatic Recovery**: Auto-retry mechanism for API failures and network issues

## Requirements

- Python 3.7 or higher
- Required Python packages:
  ```
  requests>=2.26.0
  numpy>=1.19.2
  matplotlib>=3.3.4
  scikit-learn>=0.24.2
  ```

## Installation

1. Clone the repository:
```bash
git clone https://github.com/shendengtech/bitcoin-price-monitor.git
cd bitcoin-price-monitor
```

2. Create and activate a virtual environment (optional but recommended):
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install required packages:
```bash
pip install -r requirements.txt
```

## Usage

1. Start the application:
```bash
python bitcoin_monitor_pro.py
```

2. The application will:
   - Launch a graphical interface showing real-time Bitcoin price
   - Display price predictions and trend analysis
   - Update automatically every 60 seconds
   - Log all activities to `bitcoin_monitor.log`

## Configuration

Key parameters can be modified in the code:

- `max_data_points`: Maximum number of data points to display (default: 90)
- Update interval: Can be adjusted in the `data_loop` method (default: 60 seconds)
- Polynomial degree for prediction: Set in `update_chart` method (default: 3)

## Technical Details

### Data Sources
- Primary: Binance API
- Secondary: CryptoCompare API
- Fallback: CoinGecko API

### Prediction Model
- Uses Polynomial Regression (degree 3)
- Implemented using scikit-learn
- Updates in real-time with new data points

### Error Handling
- Multiple API endpoints with failover
- Automatic retry mechanism
- Comprehensive logging system
- Graceful error recovery

## Contributing

1. Fork the repository
2. Create your feature branch:
```bash
git checkout -b feature/new-feature
```
3. Commit your changes:
```bash
git commit -am 'Add new feature'
```
4. Push to the branch:
```bash
git push origin feature/new-feature
```
5. Submit a pull request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Data provided by Binance, CryptoCompare, and CoinGecko APIs
- Built with Python's scientific computing stack
- UI implemented using Tkinter and Matplotlib

## Author

[shendengtech](https://github.com/shendengtech)

## Support

For support, please:
1. Check the [Issues](https://github.com/shendengtech/bitcoin-price-monitor/issues) page
2. Create a new issue if needed
3. Include relevant logs and error messages when reporting problems

---

*Last updated: 2025-03-03*
