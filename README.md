create table EMTL_NGN nologging nocache parallel (degree 100) as
 
SELECT a.branch_code, a.cust_ac_no,B.AC_DESC, b.account_class, a.chrg_ccy, a.trn_date, a.chrg_amount, c.customer_type, d.branch_addr3 as state
from fcubsnig.sttb_stamp_duty_tracker a, fcubsnig.sttm_cust_account b, sttm_customer c, sttm_branch d
WHERE a.VALUE_date between '02-MAR-2026' and '06-MAR-2026'
and a.CUST_AC_NO in (select cust_ac_no from fcubsnig.sttm_cust_account where CCY = 'NGN')
and a.chrg_ccy = 'NGN' 
and a.status = 'P'
and a.cust_ac_no = b.cust_ac_no
and b.branch_code = d.branch_code
and b.cust_no = c.customer_no;

select * from EMTL_NGN;

--------------------------------------------------------------------------------------------------


SELECT
    state,
    SUM(CASE WHEN customer_type = 'C' THEN 1 ELSE 0 END) AS CORPORATE,
    SUM(CASE WHEN customer_type = 'I' THEN 1 ELSE 0 END) AS INDIVIDUAL,
    SUM(CASE WHEN customer_type = 'B' THEN 1 ELSE 0 END) AS PARTNERSHIP_BUSINESS_NAME,
    COUNT(cust_ac_no) AS TRANSACTION_COUNT,
    SUM(chrg_amount) AS CUMULATIVE_AMOUNT_CHARGED
FROM EMTL_NGN
GROUP BY state
ORDER BY state;

---select * from fcubsnig.sttm_account_balance where cust_ac_no = '9983059794'
