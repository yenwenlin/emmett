import numpy as np
from scipy.optimize import fsolve
import requests

def irrFind(cashFlowVec, cashFlowPeriod, compoundPeriod):
    # Helper function to calculate the NPV at a given rate (r)
    def npv(rate):
        npv_value = 0
        for i, cash_flow in enumerate(cashFlowVec):
            # Adjust the time factor using the ratio of cashFlowPeriod to compoundPeriod
            time_factor = i * (cashFlowPeriod / compoundPeriod)
            npv_value += cash_flow / ((1 + rate) ** time_factor)
        return npv_value

    # Use fsolve to find the rate where NPV is 0 (starting with an initial guess of 0.05)
    irr_solution = fsolve(npv, 0.05)

    # Return the IRR value
    return float(irr_solution[0])

def download_file(url):
    response = requests.get(url)
    response.raise_for_status()  # 確保請求成功
    return response.text.strip().splitlines()  # 返回每行數據的列表

# 下載並讀取輸入文件
input_url = "http://mirlab.org/jang/courses/fintech/homework/2024/irr/input1000open.txt"
lines = download_file(input_url)

# 計算 IRR
calculated_irr = []
for line in lines:
    values = list(map(int, line.split()))  # 將每行數據轉換為整數列表
    cashFlowVec = values[:-2]  # 現金流向量
    cashFlowPeriod = values[-2]  # 現金流周期
    compoundPeriod = values[-1]  # 複利周期
    irr = irrFind(cashFlowVec, cashFlowPeriod, compoundPeriod)
    calculated_irr.append(f'{irr:.6f}')  # 格式化到小數點後六位

# 下載並讀取輸出文件
output_url = "http://mirlab.org/jang/courses/fintech/homework/2024/irr/output1000open.txt"
expected_irr = download_file(output_url)

# 比較計算的 IRR 與預期的 IRR
for i, (calc, exp) in enumerate(zip(calculated_irr, expected_irr)):
    print(f'Case {i + 1}: Calculated IRR: {calc}, Expected IRR: {exp}')
