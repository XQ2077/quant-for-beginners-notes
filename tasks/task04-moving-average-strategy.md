# Quant-for-Beginners Task4：移动平均线策略学习笔记

日期：2026-07-18

## 1. 今天学习的 Task

本次完成 Task4，学习第三章“移动平均线策略”。目标是用移动平均线过滤价格噪声，把“短期趋势强于长期趋势”的直觉写成可执行规则，并识别金叉、死叉和持仓状态。

## 2. 完成的课程要求

- 理解上涨趋势、下跌趋势和短期噪声的区别。
- 使用 `rolling(n).mean()` 计算简单移动平均线。
- 将 `MA5 > MA20` 写成持仓信号，并用均线差值的符号变化识别金叉和死叉。
- 将挑战标的改为 `TSLA`，绘制近 2 年均线和买卖信号图。
- 比较 MA5/MA20 与 MA10/MA30 的信号数量。
- 回答“金叉是否一定赚钱，以及为什么需要回测”的思考题。

## 3. 知识点总结

### 3.1 趋势与噪声

趋势描述价格在一段时间内的整体方向，噪声是消息、成交和短期情绪造成的日常抖动。只看原始收盘价容易被局部波动干扰，因此趋势策略需要先用平滑方法降低噪声，再把观察到的方向转成明确规则。

### 3.2 简单移动平均线

$n$ 日简单移动平均线是最近 $n$ 个收盘价的算术平均：

$$
MA_n(t)=\frac{1}{n}\sum_{i=0}^{n-1}P_{t-i}
$$

pandas 中使用 `df['Close'].rolling(n).mean()` 计算。前 $n-1$ 行因为历史数据不足会得到 `NaN`，识别信号时需要先排除这些尚未形成完整窗口的日期。

### 3.3 长短周期的取舍

短周期均线使用较少数据，贴近价格、反应更快，但容易跟随噪声频繁转向；长周期均线更平滑，可以过滤部分短期波动，但确认趋势更慢。MA5/MA20 比 MA10/MA30 更灵敏，因此通常会产生更多交叉和交易信号。

### 3.4 金叉与死叉的识别

先计算短期均线与长期均线的差值：

$$
spread_t=MA_{short}(t)-MA_{long}(t)
$$

当差值由负转正，短均线从下向上穿过长均线，形成金叉；由正转负则形成死叉。代码可以用 `np.sign(spread).diff()` 检查符号变化：结果大于 0 表示金叉，小于 0 表示死叉。

### 3.5 从图形到交易规则

最简单的双均线策略可以写为 `signal = (MA5 > MA20).astype(int)`：`1` 表示持仓，`0` 表示空仓。`signal.diff()` 或交叉标记可以识别持仓状态何时改变。把规则写成 DataFrame 列后，主观的“看起来趋势变强”就变成了可以重复运行、统计和回测的信号。

### 3.6 信号时点、交易成本与回测

均线由收盘价计算，今天收盘后才知道今天的完整信号，因此回测通常使用 `signal.shift(1)`，让信号从下一交易日生效，避免未来函数。金叉也不保证盈利：均线具有滞后性，震荡行情可能产生连续假信号；频繁换仓还会累积手续费和滑点。必须通过回测比较收益、回撤、交易次数和不同市场阶段的表现。

### 3.7 关键函数与双均线算法

#### 函数与方法速查

| 函数或方法 | 关键参数 | 返回值 | 本 Task 中的用途 |
| --- | --- | --- | --- |
| `Series.rolling()` | 窗口 `window`、最少样本数 `min_periods` | 延迟计算的 `Rolling` 对象 | 定义最近 5、10、20 或 30 个交易日的滑动窗口 |
| `Rolling.mean()` | 通常无额外参数 | 与原索引对齐的移动平均 `Series` | 把窗口内收盘价求均值得到短、长周期均线 |
| `np.sign()` | 数值数组或 `Series` | 负数映射为 -1，0 保持 0，正数映射为 1 | 把均线价差压缩为短均线位于长均线上方或下方的状态 |
| `Series.diff()` | `periods`，默认与前一行比较 | 相邻差分 `Series` | 检测符号或持仓状态是否发生改变 |
| `DataFrame.dropna(subset=...)` | `subset` 指定必须非空的列 | 清理后的新 `DataFrame` | 排除长均线窗口尚未形成时的前置空值 |
| 布尔索引 `df[condition]` | 与行索引对齐的布尔条件 | 满足条件的行 | 提取金叉、死叉对应日期和价格 |
| `Series.astype(int)` | 目标数据类型 | 转换类型后的 `Series` | 把 `True/False` 持仓条件转换为 `1/0` 信号 |
| `Series.shift(1)` | 位移期数，正数向后移动 | 索引不变、数值后移的 `Series` | 让收盘后得到的信号从下一交易日生效 |

`rolling(20).mean()` 默认要求窗口中有 20 个有效值，因此前 19 行是 `NaN`。这些空值表示“历史长度不足”，不能填成价格或信号 0 后直接参与交叉统计，否则可能产生人为的首次交易。

#### 双均线信号算法

1. 下载 TSLA 收盘价，为每组参数分别计算短周期和长周期移动平均线。
2. 先删除两条均线尚未同时形成的行，再计算价差 $spread_t=MA_{short}(t)-MA_{long}(t)$。
3. 将价差转换为状态：短均线在长均线上方记为 1，否则记为 0。
4. 对状态调用 `diff()`：从 0 变为 1 得到 `+1`，对应金叉；从 1 变为 0 得到 `-1`，对应死叉。
5. 对每组参数统计交叉次数并绘制价格、两条均线和买卖标记。
6. 真正回测时对持仓状态调用 `shift(1)`，再乘以下一交易日收益，并扣除手续费和滑点。

#### `np.sign()` 与布尔状态的差异

当前实验使用 `np.sign(spread).diff()`：状态包含 -1、0、1。如果价差恰好等于 0，路径可能先从 -1 变到 0，再从 0 变到 1，两次差分都为正，存在把“触碰”和“真正穿越”分别计数的可能。

使用 `(spread > 0).astype(int).diff()` 时，价差小于或等于 0 都属于空仓状态，只有从“不在上方”变为“在上方”才记录金叉。它更适合直接生成 0/1 持仓信号，但仍应先删除均线前置 `NaN`，以免第一条有效记录被误判为状态变化。

#### 复杂度与边界情况

对长度为 $n$ 的价格序列，pandas 的滚动均值、差值和状态检测都可以在线性时间内完成，每组参数的时间复杂度为 $O(n)$。比较 $p$ 组均线时，总时间复杂度为 $O(pn)$；如果为每组参数保存两条均线和交叉列，额外空间复杂度也是 $O(pn)$。

- 长周期越大，前置 `NaN` 越多，可用于统计的有效区间越短。
- 均线使用历史价格，信号天然滞后；参数更短只能提高灵敏度，不能消除滞后。
- 震荡行情可能频繁产生交叉，信号次数增加后，手续费和滑点会更明显。
- 未使用 `shift(1)` 就让当天收盘信号获得当天收益，会构成未来函数。
- 参数在同一段历史数据上反复优化容易过拟合，需要保留样本外区间验证。

#### 最小示例

```python
import numpy as np
import yfinance as yf

raw = yf.download(
    'TSLA', period='2y', progress=False, multi_level_index=False
).dropna()
if raw.empty:
    raise RuntimeError('未获取到 TSLA 行情')

ma = raw[['Close']].copy()
ma['MA5'] = ma['Close'].rolling(window=5, min_periods=5).mean()
ma['MA20'] = ma['Close'].rolling(window=20, min_periods=20).mean()
valid = ma.dropna(subset=['MA5', 'MA20']).copy()

spread = valid['MA5'] - valid['MA20']
sign_cross = np.sign(spread).diff()          # 当前实验的识别方式
state = (spread > 0).astype(int)
state_cross = state.diff()                   # 直接识别 0/1 状态变化

golden = valid[state_cross == 1]
death = valid[state_cross == -1]
next_day_position = state.shift(1).fillna(0)

print('金叉：', len(golden), '死叉：', len(death))
print('下一交易日持仓信号：')
print(next_day_position.tail())
```

## 4. 运行结果与学习记录

### 4.1 运行代码

```python
import warnings

import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import yfinance as yf

warnings.filterwarnings('ignore')
plt.rcParams['font.sans-serif'] = [
    'Heiti SC', 'PingFang SC', 'Microsoft YaHei', 'SimHei',
    'Noto Sans CJK SC', 'WenQuanYi Micro Hei', 'DejaVu Sans',
]
plt.rcParams['axes.unicode_minus'] = False

# 下载 TSLA 近 2 年行情
challenge_ticker = 'TSLA'
challenge_period = '2y'
tsla_raw = yf.download(
    challenge_ticker, period=challenge_period, progress=False,
    multi_level_index=False,
).dropna()
tsla_ma = tsla_raw[['Close']].copy()

# 计算两组移动平均线并统计金叉、死叉
ma_pairs = [(5, 20), (10, 30)]
signal_summary = {}

for short_window, long_window in ma_pairs:
    short_col = f'MA{short_window}'
    long_col = f'MA{long_window}'
    cross_col = f'cross_{short_window}_{long_window}'

    tsla_ma[short_col] = tsla_ma['Close'].rolling(short_window).mean()
    tsla_ma[long_col] = tsla_ma['Close'].rolling(long_window).mean()
    spread = tsla_ma[short_col] - tsla_ma[long_col]
    tsla_ma[cross_col] = np.sign(spread).diff()

    valid = tsla_ma.dropna(subset=[short_col, long_col])
    golden = valid[valid[cross_col] > 0]
    death = valid[valid[cross_col] < 0]
    signal_summary[f'MA{short_window}/MA{long_window}'] = {
        'golden': len(golden),
        'death': len(death),
        'total': len(golden) + len(death),
    }

print(f'{challenge_ticker} 共 {len(tsla_ma)} 个交易日')
for name, stats in signal_summary.items():
    print(
        f"{name}: 金叉 {stats['golden']} 次，死叉 {stats['death']} 次，"
        f"合计 {stats['total']} 次"
    )

# 绘制两组参数的均线与交叉信号
fig, axes = plt.subplots(2, 1, figsize=(14, 11), sharex=True)

for ax, (short_window, long_window) in zip(axes, ma_pairs):
    short_col = f'MA{short_window}'
    long_col = f'MA{long_window}'
    cross_col = f'cross_{short_window}_{long_window}'
    valid = tsla_ma.dropna(subset=[short_col, long_col])
    golden = valid[valid[cross_col] > 0]
    death = valid[valid[cross_col] < 0]

    ax.plot(
        tsla_ma.index, tsla_ma['Close'], color='gray',
        alpha=0.45, linewidth=1, label='收盘价',
    )
    ax.plot(
        tsla_ma.index, tsla_ma[short_col], color='tab:orange',
        linewidth=1.6, label=short_col,
    )
    ax.plot(
        tsla_ma.index, tsla_ma[long_col], color='tab:blue',
        linewidth=2, label=long_col,
    )
    ax.scatter(
        golden.index, golden['Close'], marker='^', s=95,
        color='limegreen', edgecolors='darkgreen', zorder=5,
        label='金叉 ▲',
    )
    ax.scatter(
        death.index, death['Close'], marker='v', s=95,
        color='salmon', edgecolors='darkred', zorder=5,
        label='死叉 ▼',
    )
    stats = signal_summary[f'MA{short_window}/MA{long_window}']
    ax.set_title(
        f"TSLA {short_col}/{long_col}：金叉 {stats['golden']} 次，"
        f"死叉 {stats['death']} 次"
    )
    ax.set_ylabel('价格（美元）')
    ax.legend(loc='upper left', ncol=4)
    ax.grid(True, alpha=0.3)

axes[-1].set_xlabel('日期')
fig.suptitle(
    'Task4 挑战：TSLA 双均线金叉 / 死叉参数对比',
    fontsize=15, y=1.01,
)
plt.tight_layout()
plt.show()
```

### 4.2 运行输出

以下是 2026-07-18 完成笔记时保存的运行结果。代码使用滚动时间参数 `period='2y'`，未来重新运行时，交易日数量和交叉次数会随样本窗口更新。

```text
TSLA：501 个交易日
MA5/MA20：金叉 14 次，死叉 14 次，合计 28 次
MA10/MA30：金叉 10 次，死叉 10 次，合计 20 次
```

![TSLA 双均线金叉与死叉参数对比](../assets/task04/tsla-ma-crossover-comparison.png)

### 4.3 学习记录

MA10/MA30 比 MA5/MA20 少产生 8 次交叉信号。较长周期的均线更加平滑，能够过滤一部分短期波动，但确认趋势的速度也更慢。

## 5. 学习心得

移动平均线把每天杂乱的价格变化压缩成更容易观察的趋势，双均线交叉又把这种观察转化成明确规则。不过，信号清晰不等于策略一定赚钱：均线来自历史价格，天然存在滞后；在震荡行情中，价格可能反复穿越均线，形成连续的假金叉和假死叉。更短的参数反应快但交易频繁，更长的参数稳定但可能错过行情前段。因此，不能只挑历史上看起来最漂亮的参数，还要考虑交易成本、不同市场阶段和样本外表现。

回测时还需要使用 `signal.shift(1)`，让今天收盘后得到的信号从下一交易日开始生效，避免无意中使用未来信息。手续费和滑点也可能使看似有效的频繁信号失去盈利能力。

## 6. 还没完全懂的问题

应该如何选择均线周期，才能避免为了迎合某一段历史行情而过拟合？除了比较累计收益，还应怎样结合最大回撤、交易次数、手续费和样本外测试来判断一组参数是否稳定？
