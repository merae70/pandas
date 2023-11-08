# Решения задач средней сложности '30 days of pandas' от leetcode
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| department  | varchar |
| managerId   | int     |
+-------------+---------+
```
**id** is the primary key (column with unique values) for this table.<br/>
Each row of this table indicates the name of an employee, their department, and the id of their manager.<br/>
If managerId is null, then the employee does not have a manager.<br/>
No employee will be the manager of themself.<br/>

(1) Write a solution to find managers with at least five direct reports.<br/>
Return the result table in any order.<br/>

```
def find_managers(employee: pd.DataFrame) -> pd.DataFrame:
    
    managers = employee.groupby(
        'managerId'
    ).agg(
        report=('id', 'count')
    ).query(
        "report >= 5"
    ).reset_index()['managerId']

    return employee[employee['id'].isin(managers)][['name']]
```
(2) Write a solution to find employees who have the highest salary in each of the departments.<br/>
Return the result table in any order.<br/>

```
def department_highest_salary(employee: pd.DataFrame, department: pd.DataFrame) -> pd.DataFrame:
    merged_df = employee.merge(department, left_on='departmentId', right_on='id', suffixes=('_employee', '_department'))
    
    highest_salary_df = merged_df.groupby('departmentId').apply(lambda x : x[x['salary'] == x['salary'].max()])

    result_df = highest_salary_df[['name_department', 'name_employee', 'salary']]
    result_df.columns = ['Department','Employee', 'Salary']
    return result_df
```

(3) Write a solution to find the second highest salary from the Employee table. If there is no second highest salary, return null (return None in Pandas).

```
def second_highest_salary(employee: pd.DataFrame) -> pd.DataFrame:
    unique_salaries = employee['salary'].drop_duplicates().sort_values(ascending=False)

    if len(unique_salaries) < 2:
        return pd.DataFrame({'SecondHighestSalary' : [None]})

    return pd.DataFrame({'SecondHighestSalary' : [unique_salaries.iloc[1]]})
```

(4) Write a solution to find the nth highest salary from the Employee table. If there is no nth highest salary, return null.

```
def nth_highest_salary(employee: pd.DataFrame, N: int) -> pd.DataFrame:
    unique_salaries = employee['salary'].drop_duplicates().sort_values(ascending=False)

    if N > len(unique_salaries):
        return pd.DataFrame({'getNthHighestSalary({})'.format(N) : [None]})

    return pd.DataFrame({'getNthHighestSalary({})'.format(N) : [unique_salaries.iloc[N - 1]]})
```
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| account_id  | int  |
| income      | int  |
+-------------+------+
```
**account_id** is the primary key (column with unique values) for this table.<br/>
Each row contains information about the monthly income for one bank account.<br/>

(5) Write a solution to calculate the number of bank accounts for each salary category. The salary categories are:

"Low Salary": All the salaries strictly less than $20000.<br/>
"Average Salary": All the salaries in the inclusive range [$20000, $50000].<br/>
"High Salary": All the salaries strictly greater than $50000.<br/>
The result table must contain all three categories. If there are no accounts in a category, return 0.<br/>

```
def count_salary_categories(accounts: pd.DataFrame) -> pd.DataFrame:
    return pd.DataFrame(
        {'category' : ['Low Salary', 'Average Salary', 'High Salary'],
        'accounts_count' : [
            accounts[accounts['income'] < 20_000].shape[0],
            accounts[(accounts['income'] >= 20_000) & (accounts['income'] <= 50_000)].shape[0],
            accounts[accounts['income'] > 50_000].shape[0]
        ]
        })
```
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| score       | decimal |
+-------------+---------+
```
**id** is the primary key (column with unique values) for this table.<br/>
Each row of this table contains the score of a game. Score is a floating point value with two decimal places.<br/>

(6) Write a solution to find the rank of the scores. The ranking should be calculated according to the following rules:

The scores should be ranked from the highest to the lowest.<br/>
If there is a tie between two scores, both should have the same ranking.<br/>
After a tie, the next ranking number should be the next consecutive integer value. In other words, there should be no holes between ranks.<br/>

```
def order_scores(scores: pd.DataFrame) -> pd.DataFrame:
    scores['rank'] = scores['score'].rank(method='dense', ascending=False)
    return scores.drop('id', axis=1).sort_values(by='rank')
```
