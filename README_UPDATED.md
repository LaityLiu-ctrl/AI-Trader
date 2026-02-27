#### AI-Trader: Can AI Beat the Market? with model modification

Here is a Profile for Instructing how to run the whole AI-Trader models for stock daily trading in A-share markets only
Additionally, how to modify the model into deepseek

### Environment installation

```bash
# 1. set to your clone directory
cd './AI-Trader'

# 2. Install dependencies
pip install -r requirements.txt
# Or manually install core dependencies
pip install langchain langchain-openai langchain-mcp-adapters fastmcp python-dotenv requests numpy pandas tushare


# 3. Configure environment variables after filling your API keys setting

# example template of the env file settings
    OPENAI_API_BASE=https://your-openai-proxy.com/v1 
    #e.g. for substitue into deepseek 
    # OPENAI_API_BASE="https://api.deepseek.com/v1"
    OPENAI_API_KEY=your_openai_key
    #OPENAI_API_KEY=your deepseek api key

    # 📊 Data Source Configuration
    # Only need to setup the target market data API key if you needed
    #Here tushare API token for A-share market data
    TUSHARE_TOKEN=your_tushare_token               
    
    # ⚙️ System Configuration
    RUNTIME_ENV_PATH=./runtime_env.json # Recommended to use absolute path

    # 🌐 Service Port Configuration
    MATH_HTTP_PORT=8000
    SEARCH_HTTP_PORT=8001
    TRADE_HTTP_PORT=8002
    GETPRICE_HTTP_PORT=8003
    CRYPTO_HTTP_PORT=8005

    # 🧠 AI Agent Configuration
    AGENT_MAX_STEP=30             # Maximum reasoning steps
# run below after filling all needed settings
cp .env.example .env
```



## 🌠 Running Procedure

### 🚀 Quick Start with Scripts

The Author provide convenient shell scripts in the `scripts/` directory for easy startup:
Here we only mentioned how to change the model and execute the daily trading manipulation for A-share Market


### Step 1 Model config settings

Change the enabled model for daily trading configs in ./configs/astock_config.json
```bash
#e.g. for enabling deepseek among model lists
    {
      "name": "deepseek-chat", #The original deepseek-chat-v3.2 not works, deepseek-chat is a more robust way
      "basemodel": "deepseek-chat",
      "signature": "deepseek-chat",
      "enabled": true #replace with true
    },
```

#### Step 2 run the presettled scripts
Here you need to operate two terminals which one support for the MCP servers and the other simulate the trading
```bash
# Run step by step:
# subStep 1: Prepare A-share data
# Before run the first script, check which data api you are using, if occurs error commond not found, replace with python3 as below
# get A-share date through alphavantage
python3 get_daily_price_alphavantage.py
python3 merge_jsonl_alphavantage.py
# get A-share date through for tushare
python3 get_daily_price_tushare.py
python3 merge_jsonl_tushare.py
# run script 1
bash scripts/main_a_stock_step1.sh 
#if you face with false api token
# add these two lines under the line for both function get_index_daily_data and get_daily_price_a_stock
    ts.set_token(token)
    pro._DataApi__token = token
    pro._DataApi__http_url = 'YOUR URL FOR YOUR TUSHARE API TOKEN'

# subStep 2: Start MCP services
bash scripts/main_a_stock_step2.sh
# After changing into the deepseek model, you may face with the type error, the updated base_agent_astock.py will solve the problem

"""
⚠️ Attempt 1 failed, retrying after 1.0 seconds...
Error details: Error code: 400 - {'error': {'message': 'Model Not Exist', 'type': 'invalid_request_error', 'param': None, 'code': 'invalid_request_error'}}
"""
#After successful running the script 2, create a second terminal to run script 3
# subStep 3: Run A-share trading agent
bash scripts/main_a_stock_step3.sh  
# After successful running the script 3, it will automatically invoke agent to simulate trading with given default period from 2025.10.9 to 2025.10.31 which could be personalized through changing config
# The trading only execute on trading days
```

#### Time setting for A-share market (BaseAgentAStock)
```json
{
  "agent_type": "BaseAgentAStock",  // A-share daily lines
  "market": "cn",                   
  "date_range": {
    "init_date": "2025-10-09",      // Start date
    "end_date": "2025-10-31"         // End date
  },
  "models": [
    {
      "name": "claude-3.7-sonnet",
      "basemodel": "anthropic/claude-3.7-sonnet",
      "signature": "claude-3.7-sonnet",
      "enabled": true
    }
  ],
  "agent_config": {
    "initial_cash": 100000.0        // intial fund setting：¥100,000
  },
  "log_config": {
    "log_path": "./data/agent_data_astock" 
  }
}
```

#### 🌐 Web UI
Here provide a visulaizable graph for the trading comparsion for different models
```bash
# Start web interface
bash scripts/start_ui.sh
# Visit: http://localhost:8888
```

---

### Here also provide with manual execution instruction

If you prefer to run commands manually, follow these steps:

### Step 1: Data Preparation


```bash
# 📈 Get Chinese A-share daily market data (SSE 50 Index)
cd data/A_stock

# Choosing data source from either tushare or alphavantage
#tushare
python3 get_daily_price_tushare.py
python3 merge_jsonl_tushare.py
# alphavantage
python3 get_daily_price_alphavantage.py
python3 merge_jsonl_alphavantage.py

# 📊 Daily data will be saved to: data/A_stock/merged.jsonl

```


### Step 2: Start MCP Services

```bash
cd ./agent_tools
python start_mcp_services.py
```

### Step 3: Start AI trading simulation

#### For A-Shares (SSE 50):
```bash
# 🎯 Run A-share trading
python main.py configs/astock_config.json
```

### Citation！！！！
This whole repository are only used for lecture assignment purpose with special focus on model modification instructions
Sincerely appreciate for all the contributors in AI-Trader!!🌹🌹🌹



```python
@article{fan2025ai,
  title={AI-Trader: Benchmarking Autonomous Agents in Real-Time Financial Markets},
  author={Fan, Tianyu and Yang, Yuhao and Jiang, Yangqin and Zhang, Yifei and Chen, Yuxuan and Huang, Chao},
  journal={arXiv preprint arXiv:2512.10971},
  year={2025}
}
```


<p align="center">
  <em> ❤️ Thanks for visiting ✨ AI-Trader!</em><br><br>
  <img src="https://visitor-badge.laobi.icu/badge?page_id=HKUDS.AI-Trader&style=for-the-badge&color=00d4ff" alt="Views">
</p>