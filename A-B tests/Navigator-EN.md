<div align="center">
  🇷🇺 <a href="/A-B%20tests/Navigator.md">Версия на русском</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  <a href="https://github.com/marashot96/portfolio/blob/main/README-EN.md#-skills"> ⬅️ Back to Portfolio</a>
</div>

# <div align="center"> My Skills in A/B Testing </div>

<p align="justify">
A/B testing is a key tool for product analysts to make data-driven decisions.  
This section presents theoretical foundations and metrics essential for proper planning and analysis of A/B experiments, with examples relevant to banking and finance.
</p>

<p align="justify">
<a href='https://github.com/marashot96/portfolio/blob/main/A-B%20tests/theory.md'>Here</a> you can find a detailed theoretical justification for using A/B testing.  
Below, we will focus on its practical applications.
</p>

## Practical Examples

### 1. Template for A/B Test Evaluation

#### Purpose
<div align="justify">
A/B test results are often analyzed using Python.  
To avoid rewriting the same code repeatedly, I created a Jupyter Notebook template for conducting A/B test analysis.  
Detailed instructions are included, along with a sample case to help colleagues navigate the template more easily.
</div>

### 2. Optimizing Bank Website Conversion

#### Experiment Description
- **Goal:** Increase conversion to credit card applications  
- **Groups:** Control (old design) vs Test (new design)  
- **Metric:** Conversion to application (%)  
- **Sample size:** 10,000 users per group  

Full analysis is available [here](/A-B%20tests/A-B%20test.ipynb). Summary below:

#### Results

| Group       | Users   | Conversion | 95% Confidence Interval    |
|-------------|---------|------------|-----------------------------|
| Control     | 10,000  | 12.3%      | (11.7%, 12.9%)             |
| Test        | 10,000  | 15.8%      | (15.1%, 16.5%)             |

#### Conclusions:
- **p-value < 0.001** (statistically significant)  
- **Relative uplift:** +28.5%  
- **Business effect:** +450 cards/month  

---

## 💼 Contact

If you'd like to discuss ideas, requests, or collaborations — feel free to reach out!

- 📫 [t.me/marashot96](https://t.me/marashot96)  
- 🌐 [marashot96@ya.ru](mailto:marashot96@ya.ru)

<div align="center">  <a href="https://github.com/marashot96/portfolio/blob/main/README-EN.md#-skills"> ⬅️ Back to Portfolio </a> </div>
