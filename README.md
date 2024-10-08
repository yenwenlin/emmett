from scipy.optimize import newton

# NPV function definition
def npv(rate, cashFlowVec, cashFlowPeriod, compoundPeriod):
    periods = len(cashFlowVec)
    total_value = 0
    for t in range(periods):
        # Adjust for cash flow period and compound period
        total_value += cashFlowVec[t] / (1 + rate)**(t * cashFlowPeriod / compoundPeriod)
    return total_value

# IRR calculation function
def irrFind(cashFlowVec, cashFlowPeriod, compoundPeriod):
    # Define the NPV function as a lambda for root finding
    npv_func = lambda rate: npv(rate, cashFlowVec, cashFlowPeriod, compoundPeriod)
    
    # Initial guess for IRR (5%)
    initial_guess = 0.05
    
    # Use Newton's method to find the rate where NPV is 0
    irr = newton(npv_func, initial_guess)
    
    return irr

# Example test cases from the problem description
print(f"IRR Example 1: {irrFind([-1234, 362, 548, 481], 12, 12):.6f}")  # Should return ~0.059616
print(f"IRR Example 2: {irrFind([-1234, 362, 548, 481], 12, 1):.6f}")   # Should return ~0.058047
print(f"IRR Example 3: {irrFind([-16320, -16157, -16157, -16157, -16157, -16157, 100000], 12, 12):.6f}")  # ~0.008389
print(f"IRR Example 4: {irrFind([-59356, -58762, -58762, -58762, -58762, -58762, 380000], 12, 1):.6f}")  # ~0.020738
