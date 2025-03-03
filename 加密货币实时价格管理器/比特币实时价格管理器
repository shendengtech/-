import requests
import numpy as np
import matplotlib.pyplot as plt
from sklearn.preprocessing import PolynomialFeatures
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import make_pipeline
from tkinter import Tk, Label, Frame, StringVar
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import threading
import time
from datetime import datetime
import logging
import sys

# 配置英文字体
plt.rcParams['font.sans-serif'] = ['Arial', 'DejaVu Sans']
plt.rcParams['axes.unicode_minus'] = False

class BitcoinPriceApp:
    def __init__(self):
        self.root = Tk()
        self.root.title("Bitcoin Price Monitor - Professional Edition")
        self.root.geometry("1200x900")
        
        # 初始化数据存储
        self.price_history = []
        self.time_index = []
        self.time_labels = []
        self.max_data_points = 90
        
        self.create_widgets()
        
        self.running = True
        
        # 配置日志
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(levelname)s - %(message)s',
            handlers=[
                logging.FileHandler("bitcoin_monitor.log"),
                logging.StreamHandler(sys.stdout)
            ]
        )
        self.logger = logging.getLogger(__name__)
        self.logger.info("Application initialized successfully")

    def create_widgets(self):
        # 状态栏
        self.status_var = StringVar()
        self.status_var.set("System Status: Initializing...")
        status_bar = Label(self.root, textvariable=self.status_var, 
                         font=('Arial', 10), fg="#666666", anchor='w')
        status_bar.pack(side='bottom', fill='x', padx=5, pady=5)

        # 价格显示区域
        price_frame = Frame(self.root)
        price_frame.pack(pady=15)
        
        # 实时价格
        self.price_var = StringVar()
        self.price_var.set("Fetching initial price...")
        price_label = Label(price_frame, textvariable=self.price_var,
                           font=('Arial', 28, 'bold'), fg="#2980B9")
        price_label.pack(side='left', padx=20)

        # 预测价格
        self.prediction_var = StringVar()
        self.prediction_var.set("Predicted Price: Waiting for data...")
        prediction_label = Label(price_frame, textvariable=self.prediction_var,
                               font=('Arial', 18), fg="#E74C3C")
        prediction_label.pack(side='left', padx=20)

        # 图表区域
        self.fig, self.ax = plt.subplots(figsize=(14, 8))
        self.canvas = FigureCanvasTkAgg(self.fig, master=self.root)
        self.canvas.get_tk_widget().pack(fill='both', expand=True, padx=10, pady=10)
        
        # 配置图表样式
        self.ax.set_title("Bitcoin Price Trend Analysis with Prediction", fontsize=18, pad=20)
        self.ax.set_xlabel("Time", fontsize=14)
        self.ax.set_ylabel("Price (USD)", fontsize=14)
        self.ax.grid(True, alpha=0.3)
        self.ax.set_facecolor('#F8F9F9')

    def fetch_price(self):
        """Get real-time price (enhanced version)"""
        endpoints = [
            ("Binance", "https://api1.binance.com/api/v3/ticker/price?symbol=BTCUSDT"),
            ("CryptoCompare", "https://min-api.cryptocompare.com/data/price?fsym=BTC&tsyms=USD"),
            ("CoinGecko", "https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd")
        ]

        headers = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
        }

        for retry in range(3):
            for name, url in endpoints:
                try:
                    response = requests.get(url, headers=headers, timeout=10)
                    response.raise_for_status()
                    data = response.json()
                    
                    if name == "Binance":
                        price = float(data['price'])
                    elif name == "CryptoCompare":
                        price = float(data['USD'])
                    elif name == "CoinGecko":
                        price = float(data['bitcoin']['usd'])
                    
                    self.logger.info(f"Successfully fetched price from {name}: {price}")
                    return price
                except Exception as e:
                    self.logger.warning(f"API request failed ({name}, retry {retry+1}): {str(e)}")
                    time.sleep(2)
        
        self.logger.error("All API requests failed")
        return None

    def update_chart(self):
        """Update chart with prediction"""
        try:
            self.ax.clear()
            
            if len(self.price_history) >= 10:
                # 准备数据
                X = np.array(self.time_index).reshape(-1, 1)
                y = np.array(self.price_history)
                
                # 创建多项式回归模型
                degree = 3
                model = make_pipeline(
                    PolynomialFeatures(degree),
                    LinearRegression()
                )
                
                # 训练模型
                model.fit(X, y)
                
                # 生成预测曲线点
                X_fit = np.linspace(min(self.time_index), max(self.time_index) + 60, 100).reshape(-1, 1)
                y_fit = model.predict(X_fit)
                
                # 预测下一个点
                next_time = max(self.time_index) + 15  # 15秒后的预测
                prediction = model.predict([[next_time]])[0]
                
                # 绘制实际价格
                self.ax.plot(self.time_index, self.price_history,
                            color='#3498DB', linewidth=2.5,
                            marker='o', markersize=6,
                            label='Actual Price')
                
                # 绘制趋势线
                self.ax.plot(X_fit, y_fit, '--', color='#F39C12',
                            alpha=0.8, linewidth=2, label='Trend Line')
                
                # 绘制预测点
                self.ax.scatter(next_time, prediction,
                              color='#E74C3C', s=100, zorder=5,
                              label=f'Predicted: ${prediction:,.2f}')
                
                self.prediction_var.set(f"Predicted Price: ${prediction:,.2f}")
                
            else:
                self.ax.plot(self.time_index, self.price_history,
                            color='#3498DB', marker='o')
                self.prediction_var.set(f"Collecting data... ({len(self.price_history)}/10)")

            # 图表美化
            self.ax.set_xticks(self.time_index[::2])  # 每隔一个显示时间标签
            self.ax.set_xticklabels(self.time_labels[::2], rotation=45, ha='right')
            self.ax.legend(loc='upper left', prop={'size': 12})
            self.ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f"${x:,.0f}"))
            self.ax.grid(True, alpha=0.3)
            self.fig.tight_layout()
            self.canvas.draw()
            
        except Exception as e:
            self.logger.error(f"Chart update error: {str(e)}")
            self.prediction_var.set("Prediction Error")

    def data_loop(self):
        """Data update thread"""
        start_time = time.time()
        while self.running:
            try:
                price = self.fetch_price()
                current_time = datetime.now()
                
                if price:
                    # 记录数据
                    self.price_history.append(price)
                    timestamp = time.time() - start_time
                    self.time_index.append(timestamp)
                    self.time_labels.append(current_time.strftime("%H:%M:%S"))
                    
                    # 保持数据长度
                    if len(self.price_history) > self.max_data_points:
                        self.price_history.pop(0)
                        self.time_index.pop(0)
                        self.time_labels.pop(0)
                    
                    # 更新界面
                    self.price_var.set(f"Current Price: ${price:,.2f}")
                    self.update_chart()
                    
                    self.status_var.set(
                        f"Last Updated: {current_time.strftime('%Y-%m-%d %H:%M:%S')} | "
                        f"Data Points: {len(self.price_history)} | "
                        f"Interval: 60s"
                    )
                else:
                    self.status_var.set("Price fetch failed - Retrying...")
                
            except Exception as e:
                self.logger.error(f"Data loop error: {str(e)}")
                self.status_var.set("System Error - Check logs for details")
            
            time.sleep(15)

    def run(self):
        """Launch application"""
        try:
            data_thread = threading.Thread(target=self.data_loop, daemon=True)
            data_thread.start()
            
            self.root.protocol("WM_DELETE_WINDOW", self.safe_exit)
            self.root.mainloop()
        except Exception as e:
            self.logger.critical(f"Application crash: {str(e)}")
            sys.exit(1)

    def safe_exit(self):
        """Safe exit"""
        self.running = False
        self.root.destroy()
        self.logger.info("Application exited safely")

if __name__ == "__main__":
    app = BitcoinPriceApp()
    app.run()
