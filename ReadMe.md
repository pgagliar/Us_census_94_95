#US Census

#Vocabulary:

-‘Not in universe’ means not concern by the census.\n
Example: children won’t be ask if they have a job.\n

-The wage per hour is multiplied by 100.

-MSA:metropolitan statistical area (MSA) is a geographical region with a relatively high population density at its core and close economic ties throughout the area.

#Dataset:

-199523 training examples
-99762 testing examples
-41 features

1.First model:
The columns with missing values are dropped:

-hispanic_origin
-state_of_previous_residence
-migration_codechange_in_msa
-migration_codechange_in_reg
-migration_codemove_within_reg
-migration_prev_res_in_sunbelt
-country_of_birth_father
-country_of_birth_mother
-country_of_birth_self

So we used only columns with non missing values.
There are 31 features.

Among those 31 features, the nominal features are one-hot-encoded and continuous feature stay the same way.
We end up with 287 features.

2. We apply a chi2 test on the 185 features and we can see that the fifteen most correlated features are:
Feature name , chi2 score, p value
('capital_gains', 586898877.91127098, 0.0)
('dividends_from_stocks', 122871165.9116621, 0.0)
('capital_losses', 8590610.6017821431, 0.0)
('weeks_worked_in_year', 353029.90749081556, 0.0)
('wage_per_hour', 163659.07194978738, 0.0)
('industry_recode', 163285.42845950171, 0.0)
('age', 53035.765099071265, 0.0)
('num_persons_worked_for_employer', 28292.308625991805, 0.0)
('major_occupation_code=Executive admin and managerial', 10915.510123595772, 0.0)
('major_occupation_code=Professional specialty', 8394.8884507486982, 0.0)
('education=Prof school degree (MD DDS DVM LLB JD)', 7049.3177781129143, 0.0)
('education=Masters degree(MA MS MEng MEd MSW MBA)', 6996.231329892642, 0.0)
('detailed_household_and_family_stat=Householder', 6666.4239881249277, 0.0)
('education=Bachelors degree(BA AB BS)', 6221.937640096693, 0.0)
('detailed_household_summary_in_household=Householder', 5616.1853341942779, 0.0)


3.We use a logistic regression to predict the target:
The accuracy of the model is :
0.9535

The confusion matrix is computed:
array([[92694,   882],
       [ 3760,  2426]])


4.Scaling features:
Scaling continuous features should help the optimisation of the error_function.\n
But here, it has no effect on the classification result.\n

5.Complete missing value:

We can try to add more feature to our model to lower, to lower the bias.\n
So a new feature hispanic_simplified is created from hispanic_origin.\n

The process to create 'hispanic_simplified’:\n

if (hispanic_origin belongs to [‘All other','Do not know’]):\n
 'hispanic_simplified’=yes\n
Elif (hispanic_origin belongs to ['Central or South American','Mexican (Mexicano)','Mexican-American','Other Spanish','Puerto Rican','Cuban','Chicano’]):\n
'hispanic_simplified'no\n
Elif (the person or one of the parents come from a country in this list ['Mexico','Puerto-Rico','Ecuador','Guatemala','Nicaragua','Guatemala','Nicaragua','Cuba','Dominican-Republic','El-Salvador’]):
	'hispanic_simplified’=yes\n
else
	'hispanic_simplified’=no\n

6. Create new features:

When the weights of the logistic regression are displayed, we can see that the most important features are:
-education
-sex
-class of worker
-major_industry_code
-Householder
-industry_recode
-occupation_recode

But features that should be discriminant according to the chi2 test are practically useless:
-capital_gains
-dividends_from_stocks
-weeks_worked_in_year
-wage_per_hour

So we create two new features:
gain_total=capital_gains - capital_losses + dividends_from_stocks
income=wage_per_hour*weeks_worked_in_year

Unfortunately it has no real effect on the classification accuracy.
