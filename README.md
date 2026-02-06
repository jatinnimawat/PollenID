# Pollen ID - AI-Powered Pollen Intelligence Platform

A production-ready MVP that combines real-time pollen data from Google's Pollen API with AI-driven health insights and LSTM-based forecasting.

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![Python: 3.9+](https://img.shields.io/badge/Python-3.9+-green.svg)
![Framework: Streamlit](https://img.shields.io/badge/Framework-Streamlit-red.svg)

## Features

- **Real-Time Pollen Tracking**: Live pollen indices (Grass, Tree, Weed) via Google Pollen API
- **AI-Powered Forecasting**: LSTM-simulated 7-day predictions with interactive visualizations
- **Health Assistant**: RAG-based chat interface using GPT-4o for personalized health advice
- **Advanced Analytics**: Risk assessment, trend analysis, and distribution charts
- **Professional UI**: Financial services-grade design with Tailwind-inspired color palette
- **Responsive Design**: Optimized for desktop and mobile devices

## Quick Start

### Prerequisites

- Python 3.9 or higher
- Google Pollen API Key ([Get it here](https://console.cloud.google.com))
- OpenAI API Key ([Get it here](https://platform.openai.com))

### Local Installation

1. **Clone the repository**
```bash
git clone <your-repo-url>
cd pollen-id
```

2. **Install dependencies**
```bash
pip install -r requirements.txt
```

3. **Run the application**
```bash
streamlit run app.py
```

4. **Configure API Keys**
   - Open the app in your browser (typically http://localhost:8501)
   - Enter your Google Pollen API key in the sidebar
   - Enter your OpenAI API key in the sidebar
   - Enter a location (zip code or city name)
   - Click "Analyze Pollen Levels"

## Deploying to Streamlit Cloud

### Step 1: Prepare Your Repository

1. Push your code to GitHub
2. Ensure `app.py` and `requirements.txt` are in the root directory

### Step 2: Deploy on Streamlit Cloud

1. Go to [share.streamlit.io](https://share.streamlit.io)
2. Sign in with GitHub
3. Click "New app"
4. Select your repository
5. Set the main file path to `app.py`
6. Click "Deploy"

### Step 3: Configure Secrets (Optional but Recommended)

Instead of entering API keys in the UI, you can use Streamlit secrets:

1. In Streamlit Cloud, go to your app settings
2. Click "Secrets"
3. Add your keys:

```toml
GOOGLE_API_KEY = "your-google-api-key-here"
OPENAI_API_KEY = "your-openai-api-key-here"
```

4. Update `app.py` to read from secrets:

```python
# In the sidebar section, replace:
google_api_key = st.secrets.get("GOOGLE_API_KEY", "")
openai_api_key = st.secrets.get("OPENAI_API_KEY", "")
```

## API Key Setup

### Google Pollen API

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create a new project or select an existing one
3. Enable the "Pollen API"
4. Create credentials (API Key)
5. Copy the API key

**Important**: The Google Pollen API may have usage limits. Check the [pricing page](https://cloud.google.com/pollen/pricing) for details.

### OpenAI API

1. Go to [OpenAI Platform](https://platform.openai.com)
2. Sign in or create an account
3. Navigate to API Keys
4. Create a new secret key
5. Copy the API key

**Cost Estimate**: The Health Assistant uses GPT-4o, which costs approximately $0.01-0.02 per conversation turn.

## Project Structure

```
pollen-id/
│
├── app.py                 # Main Streamlit application
├── requirements.txt       # Python dependencies
├── README.md             # This file
├── .gitignore            # Git ignore rules
└── .streamlit/
    └── config.toml       # Streamlit configuration (optional)
```

## Architecture Overview

### Data Flow

```
User Input (Location)
    ↓
Geocoding (geopy) → Lat/Long
    ↓
Google Pollen API → Real-time pollen data
    ↓
Data Processing (pandas)
    ↓
┌─────────────────────┬──────────────────────┬────────────────────┐
│                     │                      │                    │
│  Forecast Tab       │  Health Assistant    │  Data Tab          │
│  ----------------   │  ------------------  │  --------------    │
│  • LSTM Simulation  │  • RAG Pattern       │  • Raw Data View   │
│  • Plotly Charts    │  • GPT-4o Engine     │  • JSON Response   │
│  • Trend Analysis   │  • Context Injection │  • Export Options  │
│                     │                      │                    │
└─────────────────────┴──────────────────────┴────────────────────┘
```

### Key Components

1. **Geocoding Layer** (`get_coordinates`)
   - Converts user input to lat/long
   - Uses Nominatim (OpenStreetMap)
   - Handles US-biased queries

2. **API Integration** (`get_pollen_data`)
   - Google Pollen API client
   - Error handling for 4xx/5xx responses
   - 10-second timeout protection

3. **AI Forecasting** (`simulate_lstm_forecast`)
   - Simulates LSTM model outputs
   - Trend-based predictions with random walk
   - 7-day forecast generation

4. **RAG Health Assistant** (`get_ai_health_advice`)
   - Retrieval: Current pollen data as context
   - Augmentation: Structured prompt engineering
   - Generation: GPT-4o with health advisor persona

5. **Visualization** (Plotly)
   - Interactive time-series charts
   - Dual-axis for historical vs. forecast
   - Color-coded risk levels

## UI/UX Design

### Color Palette

- **Primary Blue**: `#1E3A8A` - Trust, professionalism
- **Secondary Blue**: `#3B82F6` - Interactive elements
- **Success Green**: `#10B981` - Low risk indicators
- **Warning Yellow**: `#F59E0B` - Medium risk
- **Danger Red**: `#EF4444` - High risk
- **Background**: `#F8FAFC` - Clean, modern

### Design Principles

- **Minimalism**: Clean layouts, ample whitespace
- **Clarity**: Clear data hierarchy, intuitive navigation
- **Accessibility**: High contrast ratios, readable fonts
- **Responsiveness**: Mobile-first design approach

## Customization Guide

### Adding Your Own LSTM Model

Replace the `simulate_lstm_forecast` function:

```python
def lstm_forecast(current_data: pd.DataFrame) -> pd.DataFrame:
    """
    Production LSTM forecasting using your trained model.
    """
    import tensorflow as tf
    
    # Load your trained model
    model = tf.keras.models.load_model('path/to/your/model.h5')
    
    # Prepare input sequences
    # ... your preprocessing logic
    
    # Generate predictions
    predictions = model.predict(input_sequences)
    
    # Format output
    # ... your postprocessing logic
    
    return forecast_df
```

### Modifying Risk Thresholds

Edit the `get_risk_level` function:

```python
def get_risk_level(index: int) -> Tuple[str, str]:
    if index == 0:
        return "None", COLORS['success_green']
    elif index <= 1:  # Lower threshold for sensitive users
        return "Low", COLORS['success_green']
    # ... custom logic
```

### Adding New Pollen Types

If Google adds new pollen types, update:

1. `parse_pollen_data`: Add to `type_mapping`
2. Visualization functions: Add to `pollen_types` list
3. Color mappings: Define new colors

## Troubleshooting

### Common Issues

**Issue**: "API Key Error: Invalid or missing Google Pollen API key"
- **Solution**: Verify your API key is correct and the Pollen API is enabled in Google Cloud Console

**Issue**: "Geocoding service error"
- **Solution**: Check your internet connection. Nominatim has rate limits (1 req/sec)

**Issue**: "OpenAI API Error: Rate Limit"
- **Solution**: Wait a moment and try again. Consider upgrading your OpenAI plan for higher limits

**Issue**: Charts not rendering
- **Solution**: Clear browser cache or try a different browser. Ensure Plotly is installed correctly

### Debug Mode

Enable debug logging:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## Performance Optimization

### For Large-Scale Deployments

1. **Caching**: Use `@st.cache_data` for API responses
```python
@st.cache_data(ttl=3600)  # Cache for 1 hour
def get_pollen_data(lat, lng, api_key):
    # ... existing code
```

2. **Database Integration**: Store historical data
```python
import sqlite3
# Store pollen data in local SQLite database
```

3. **Asynchronous API Calls**: Use `asyncio` for parallel requests

4. **CDN for Static Assets**: Host images/CSS externally

## Testing

### Manual Testing Checklist

- [ ] Valid zip code → Successful geocoding
- [ ] Invalid location → Appropriate error message
- [ ] Missing API key → Clear warning
- [ ] 3-day forecast → Chart renders correctly
- [ ] Health chat → AI responses are relevant
- [ ] Feedback buttons → Confirmation messages
- [ ] Mobile view → Responsive layout

### Automated Testing (Future Enhancement)

```python
# tests/test_app.py
import pytest
from app import get_coordinates, get_risk_level

def test_get_coordinates():
    coords = get_coordinates("10001")
    assert coords is not None
    assert len(coords) == 2

def test_risk_level():
    risk, color = get_risk_level(5)
    assert risk == "High"
```

## Roadmap

- [ ] User authentication and personalized dashboards
- [ ] Email/SMS alerts for high pollen days
- [ ] Integration with wearable devices
- [ ] Multi-language support
- [ ] Historical data analysis (30-day trends)
- [ ] Export reports to PDF
- [ ] Mobile app (React Native)

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- **Google Pollen API**: For providing comprehensive pollen data
- **OpenAI**: For GPT-4o and AI capabilities
- **Streamlit**: For the excellent web framework
- **Plotly**: For beautiful, interactive visualizations

## 📧 Support

For issues, questions, or feedback:
- Open an issue on GitHub
- Contact: [your-email@example.com]

---

**Built with ❤️ for allergy sufferers worldwide**
