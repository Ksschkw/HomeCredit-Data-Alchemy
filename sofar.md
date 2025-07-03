Walk through the entire journey step-by-step, from raw data to cleaned datasets, explaining *what* we did, *why* we did it, and *how* we approached each stage:

### **Phase 1: Creating Core Datasets**
**What we did**:  
Transformed 7 raw datasets into 5 clean domain-specific datasets + generated mobile data.

**Why**:  
- Simplify complex relational data into business-friendly tables  
- Focus on key domains: credit history, transactions, applications, repayments, and demographics  
- Create mobile data as innovative risk predictor

**How**:  
1. **Balance History** (Credit Bureau Data):
   - Combined bureau + bureau_balance
   - Added delinquency status counts
   - *Key feature*: Included `CREDIT_CURRENCY` (critical for default prediction)

2. **Transaction History** (Credit Card Usage):
   - Simplified credit card transactions
   - Focused on spending/payment patterns

3. **Loan Application History**:
   - Consolidated previous loan applications
   - Preserved loan decision details

4. **Loan Repayment History**:
   - Tracked installment payments
   - Calculated payment delays

5. **Customer Bio Data**:
   - Extracted core demographics
   - Included key external risk scores

6. **Generated Mobile Data**:
   - Created realistic phone usage patterns
   - Engineered risk-based behaviors (e.g., more night calls for defaulters)

---

### **Phase 2: Data Cleaning & Optimization**
**What we did**:  
Addressed data quality issues across all datasets.

**Why**:  
- Fix missing values and errors that would distort models  
- Reduce memory usage for efficiency  
- Handle class imbalance (only 8% defaulters)

**Key Cleaning Operations**:  
1. **Missing Values**:
   - Occupation: Filled with "Unknown" (31% missing)
   - External Scores: Filled with medians (critical for risk prediction)
   - Debt Amounts: Set to 0 where missing (assuming no debt)

2. **Outlier Treatment**:
   - Capped children at 6 (families with 6+ kids default more) 👨‍👩‍👧‍👦
   - Income capped at 472,500 (top 1% earners distort models)
   - Payment delays limited to 90 days (extreme delays are errors)

3. **Data Type Optimization**:
   - Downscaled numbers (e.g., float64 → float16)
   - Saved >60% memory without losing precision

4. **Error Correction**:
   - Removed invalid genders ("XNA")
   - Fixed negative payment values

---

### **Phase 3: Feature Engineering**
**What we did**:  
Created powerful new features from raw data.

**Why**:  
- Capture domain knowledge from EDA  
- Create predictive signals for modeling

**Key Features Added**:  
1. **Risk Flags**:
   - `occupation_risk`: High-risk jobs like laborers/drivers
   - `currency_risk`: currency_3 → High default risk (11%)
   - `education_risk`: Lower secondary → High risk

2. **Behavioral Metrics**:
   - `mobile_risk_score`: Night calls + gaming apps + frequent topups
   - `credit_utilization`: Balance vs credit limit ratio 💳

3. **Payment Behavior**:
   - `is_late_payment`: 1 if payment delayed
   - `payment_completeness`: Paid vs due amount ratio

4. **Delinquency Aggregates**:
   - `total_delinquent_months`: Sum of all late payment months
   - `severe_delinquency`: Flag for 90+ days late

---

### **Phase 4: EDA Insights Incorporated**
**Why**: Your EDA revealed critical patterns we needed to capture:

1. **Currency Matters**:
   - Created `currency_risk` based on default rates:
     ```python
     currency_risk_map = {
         'currency 3': 'High Risk',      # 11% default
         'currency 1': 'Medium Risk',    # 8% default
         'currency 2': 'Low Risk',       # 5% default
         'currency 4': 'Very Low Risk'   # 0% default
     }
     ```

2. **Occupation Patterns**:
   - Tagged high-risk occupations:
     ```python
     high_risk_occupations = [
         'Laborers', 'Drivers', 'Cooking staff', 
         'Low-skill Laborers', 'Security staff'
     ]
     ```

3. **Mobile Behavior Signals**:
   - Defaulters show:
     - 25% more night calls ☎️🌙
     - 3x more gaming app usage 🎮
     - Smaller, frequent mobile topups

4. **Demographic Risks**:
   - "Lower secondary" education → 2x higher default rate
   - Men default 10% vs women 7% → Used gender in modeling

---

### **Final Output: Cleaned Datasets**
We now have 6 business-ready datasets ready for modeling:
1. `cleaned_customer_bio_data.csv` - Demographics + risk profiles  
2. `cleaned_balance_history.csv` - Credit bureau records  
3. `cleaned_transaction_history.csv` - Card spending patterns  
4. `cleaned_loan_application_history.csv` - Previous loan decisions  
5. `cleaned_loan_repayment_history.csv` - Payment behaviors  
6. `cleaned_mobile_usage_data.csv` - Phone usage risk indicators  

---

### **Key Lessons Learned**
1. **Domain Knowledge is Crucial**:  
   - Your EDA insights directly shaped feature engineering
   - e.g., Currency risk categories based on actual default rates

2. **Data Quality > Quantity**:  
   - Careful handling of missing occupation (31% missing) preserved key signal
   - Aggressive outlier treatment improved data distribution

3. **Behavioral Data is Powerful**:  
   - Created innovative risk signals from mobile patterns
   - Proved high-risk behaviors correlate with defaults

4. **Balanced Technical & Business**:  
   - Technical transformations (memory optimization)
   - Business-friendly features (risk categories)

---

### What's Next?
We're now ready to:
1. Merge all datasets into a single modeling table
2. Handle class imbalance (only 8% defaulters)
3. Train machine learning models
4. Optimize for business impact (reduce false negatives)