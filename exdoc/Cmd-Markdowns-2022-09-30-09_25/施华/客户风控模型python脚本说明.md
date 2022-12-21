# 客户风控模型python脚本说明

标签（空格分隔）： 施华

---

# **1.文件说明**
## **企业静态风险评级脚本**
+ 核心脚本：enterprise_static_riskscoring.py
+ 输入数据：company.csv(公司表),ralph_settle.csv(结算表),ralph_normal_task.csv(小额任务表),ralph_beyong_task.csv(大额任务表),武宁客户评估.xlsx，1-6月武宁客户回访数据.xlsx，person.csv(人员表),industry.csv(行业表)
+ 输出数据：company_score_df.xlsx(企业静态风险评级)

## **员工分类模型训练脚本**
+ 核心脚本：customer_classifier_train.py
+ 输入数据：1-6月武宁客户回访数据.xlsx
+ 输出数据：customer_classifier_model.m(预训练模型)

## **抽检名单生成脚本**
+ 核心脚本：person_check.py
+ 输入数据：company_score_df.xlsx(企业静态风险评级),customer_classifier_model.m(预训练模型),company.csv(公司表),ralph_settle.csv(结算表),ralph_normal_task.csv(小额任务表),ralph_beyong_task.csv(大额任务表),person.csv(人员表),industry.csv(行业表)
+ 输出数据：抽检名单(存在字典中，可在控制台中查看)

## **根据回访结果动态调整企业风险评级概率脚本**
+ 核心脚本:dynamic_enterprise_probability.py
+ 输入数据：company_score_df.xlsx(企业静态风险评级)，1-6月武宁客户回访数据.xlsx(**该数据只是为了调试代码，用了训练数据**)
+ 输出数据：company_newscore_df.xlsx(企业动态风险评级)

# 2.算法思路图

![脚本流程图.png-410.1kB][1]


  [1]: http://static.zybuluo.com/tulip0216/2yrwu2ewyvdnhvzaej11l9ti/%E8%84%9A%E6%9C%AC%E6%B5%81%E7%A8%8B%E5%9B%BE.png