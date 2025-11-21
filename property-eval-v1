import streamlit as st
import numpy_financial as npf

st.title("🏠 Property Investment Analyser V2")

# --- SIDEBAR INPUTS ---
st.sidebar.header("1. Purchase Details")
price = st.sidebar.number_input("Purchase Price ($)", value=475000)
rent_wk = st.sidebar.number_input("Weekly Rent ($)", value=520)
vacancy = st.sidebar.slider("Vacancy Rate (%)", 0, 10, 4) / 100

st.sidebar.header("2. Loan Details")
lvr = st.sidebar.slider("LVR (%)", 0, 100, 90) / 100
rate = st.sidebar.number_input("Interest Rate (%)", value=6.24) / 100
loan_type = st.sidebar.selectbox("Loan Type", ["Principal & Interest", "Interest Only"])
salary = st.sidebar.number_input("Your Annual Salary ($)", value=120000)

st.sidebar.header("3. Expenses")
rates = st.sidebar.number_input("Annual Rates/Water ($)", value=3000)
strata = st.sidebar.number_input("Annual Strata ($)", value=3000)
mgmt_fee = st.sidebar.number_input("Mgmt Fee (%)", value=8.8) / 100
maint_rate = st.sidebar.number_input("Maint. Buffer (%)", value=1.0) / 100

# --- CALCULATIONS ---
gross_rent_yr = rent_wk * 52
effective_rent = gross_rent_yr * (1 - vacancy)
loan_amt = price * lvr
deposit = price * (1 - lvr)
stamp_duty = price * 0.04  # Est
setup_costs = 3000
cash_required = deposit + stamp_duty + setup_costs

# Expenses
annual_mgmt = effective_rent * mgmt_fee
annual_maint = price * maint_rate
total_expenses = rates + strata + annual_mgmt + annual_maint

# Loan
if loan_type == "Principal & Interest":
    monthly_pmt = npf.pmt(rate/12, 30*12, -loan_amt)
    annual_repay = monthly_pmt * 12
    interest_component = loan_amt * rate # Approx for Year 1 tax
else:
    annual_repay = loan_amt * rate
    interest_component = annual_repay

# Cash Flow Pre-Tax
cf_pre_tax = effective_rent - total_expenses - annual_repay

# Tax Logic
depreciation = 6000 # Est
taxable_income = effective_rent - total_expenses - interest_component - depreciation
tax_bracket = 0.325
if salary > 120000: tax_bracket = 0.37
if salary > 180000: tax_bracket = 0.45
tax_refund = abs(taxable_income) * tax_bracket if taxable_income < 0 else 0

cf_post_tax = cf_pre_tax + tax_refund

# --- DISPLAY RESULTS ---
st.header("💰 The Verdict")

col1, col2, col3 = st.columns(3)
col1.metric("Cash Required", f"${cash_required:,.0f}")
col2.metric("Weekly Cost (Pre-Tax)", f"${cf_pre_tax/52:,.0f}")
col3.metric("Weekly Cost (Post-Tax)", f"${cf_post_tax/52:,.0f}", delta_color="inverse")

st.divider()

st.subheader("📉 Cash Flow Breakdown")
st.write(f"**Effective Rent:** ${effective_rent:,.0f}")
st.write(f"**- Operating Expenses:** ${total_expenses:,.0f}")
st.write(f"**- Loan Repayments:** ${annual_repay:,.0f}")
st.write(f"**= Pre-Tax Cash Flow:** ${cf_pre_tax:,.0f}")
st.write(f"**+ Tax Refund:** ${tax_refund:,.0f}")
st.write(f"**= NET ANNUAL CASH FLOW:** ${cf_post_tax:,.0f}")

if cf_post_tax > 0:
    st.success("✅ This property is POSITIVE Cash Flow!")
else:
    st.warning(f"⚠️ This property costs you ${abs(cf_post_tax/52):.0f} per week to hold.")
