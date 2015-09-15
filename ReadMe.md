#US Census

###Goal:

Prediction task is to determine the income level for the person represented by the record.  Incomes have been binned at the $50K level to present a binary classification problem.

###Dataset:

- 199523 training examples<br />
- 99762 testing examples<br />
- 41 features<br />

###Notes:

- ‘Not in universe’ means not concern by the census.<br />
Example: children won’t be ask if they have a job.

- The wage per hour is multiplied by 100.

- MSA: metropolitan statistical area (MSA) is a geographical region with a relatively high population density at its core and close economic ties throughout the area.

##1. First model:
The columns with missing values are dropped:

- hispanic_origin
- state_of_previous_residence
- migration_codechange_in_msa
- migration_codechange_in_reg
- migration_codemove_within_reg
- migration_prev_res_in_sunbelt
- country_of_birth_father
- country_of_birth_mother
- country_of_birth_self

In the end we used 31 features, with non missing values.<br />
Among those 31 features, the nominal features are one-hot-encoded and continuous feature stay the same way.<br />
We end up with 287 features.

##2. We apply a Anova F-test on the 7 continuous features:
Feature name , F-test score, p value<br />

- (‘weeks_worked_in_year', 22099.531778511238, 0.0)
- (‘capital_gains', 18354.25948439791, 0.0)
- (‘num_persons_worked_for_employer', 15832.132400868035, 0.0)
- (‘dividends_from_stocks', 9845.2911708393294, 0.0)
- (‘total_gain', 7073.1696675453077, 0.0)
- (‘capital_losses', 6651.2822134114776, 0.0)
- (‘age', 5666.315940942015, 0.0)

The p-values of those features are 0 so they should be correlated with the target.

##3. We use a logistic regression to predict the target:

The accuracy of the model is 0.9535 pct

The confusion matrix is computed:<br />
[92694,   882],<br />
[ 3760,  2426]


##4. Scaling features:

Scaling continuous features should help the optimisation of the error_function.
But here, it has no effect on the classification result.

##5. Complete missing value:

Maybe our model is to simple so we can try to add more feature to our model.
New feature hispanic_simplified is created from hispanic_origin.

The process to create 'hispanic_simplified’ is:<br />

If a person fills one of those conditions, he has hispanic origins:<br />

First let’s define hispanic_countries as [‘Mexico','Puerto-Rico','Ecuador','Guatemala','Nicaragua','Guatemala','Nicaragua','Cuba','Dominican-Republic','El-Salvador’]

Conditions:<br />

* c1: hispanic_origin is equal to ['Central or South American','Mexican (Mexicano)','Mexican-American','Other Spanish','Puerto Rican','Cuban','Chicano’]

* c2:Person is born in one of the hispanic countries

* c3:Person’s father is born in one of the hispanic countries

* c4:Person’s mother is born in one of the hispanic countries

Unfortunately it has no real effect on the classification accuracy: 0.9534 pct

##6. Create new features:

When the weights of the logistic regression are displayed, we can see that the most important features are:
- education
- sex
- class of worker
- major_industry_code
- householder
- industry_recode
- occupation_recode

But features that should be discriminant according to the Anova test are practically useless:
- capital_gains
- dividends_from_stocks
- weeks_worked_in_year
- wage_per_hour

So we create two new features:<br />
total_gain=capital_gains - capital_losses + dividends_from_stocks<br />
income=wage_per_hour*weeks_worked_in_year

Unfortunately it has no real effect on the classification accuracy: 0.9537 pct<br />
‘income’ and ‘total_gain’ have coefficient in the logistic regression which are closed to 0


