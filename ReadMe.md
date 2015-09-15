#US Census

###Goal:



###Dataset:

- 199523 training examples<br />
- 99762 testing examples<br />
- 41 features<br />

###Notes:

- ‘Not in universe’ means not concern by the census.<br />
Example: children won’t be ask if they have a job.

- The wage per hour is multiplied by 100.

- MSA:metropolitan statistical area (MSA) is a geographical region with a relatively high population density at its core and close economic ties throughout the area.

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

So we used only columns with non missing values.
There are 31 features.

Among those 31 features, the nominal features are one-hot-encoded and continuous feature stay the same way.
We end up with 287 features.

##2. We apply a Anova F-test on the 7 continuous features:<br />
Feature name , F-test score, p value<br />

- (‘weeks_worked_in_year', 22099.531778511238, 0.0)
- (‘capital_gains', 18354.25948439791, 0.0)
- (‘num_persons_worked_for_employer', 15832.132400868035, 0.0)
- (‘dividends_from_stocks', 9845.2911708393294, 0.0)
- (‘total_gain', 7073.1696675453077, 0.0)
- (‘capital_losses', 6651.2822134114776, 0.0)
- (‘age', 5666.315940942015, 0.0)

Their p-value=0 so those features are correlated with the target.

##3. We use a logistic regression to predict the target:

The accuracy of the model is 0.9535 pct

The confusion matrix is computed:<br />
[92694,   882],<br />
[ 3760,  2426]


##4. Scaling features:

Scaling continuous features should help the optimisation of the error_function.
But here, it has no effect on the classification result.

##5. Complete missing value:

We can try to add more feature to our model to lower, to lower the bias.
So a new feature hispanic_simplified is created from hispanic_origin.

The process to create 'hispanic_simplified’:
If a person has one of these condition, he has hispanic origins:

hispanic_countries=[‘Mexico','Puerto-Rico','Ecuador','Guatemala','Nicaragua','Guatemala','Nicaragua','Cuba','Dominican-Republic','El-Salvador’]

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

So we create two new features:
total_gain=capital_gains - capital_losses + dividends_from_stocks
income=wage_per_hour*weeks_worked_in_year

Unfortunately it has no real effect on the classification accuracy: 0.9537 pct<br />
‘income’ and ‘total_gain’ have coefficient in the logistic regression which are closed to 0


