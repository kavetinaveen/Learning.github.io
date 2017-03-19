{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Basics of Linear Regression"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Contents\n",
    "1. Simple Linear Regression (SLR)\n",
    "2. Assumptions of SLR\n",
    "4. How to check the assumptions?\n",
    "5. What happens, if any of the assumption voilates?\n",
    "6. Multiple Linear Regression (MLR)\n",
    "7. Any extra assumptions for MLR?"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 1. Simple Linear Regression"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Regression is the process of building a relationship between a dependent variable and set of independent variables. Linear Regression restricts this relationship to be linear in terms of coefficients. In SLR, we consider only one independent variable.\n",
    "\n",
    "### Example: The Waist Circumference – Adipose Tissue data\n",
    "\n",
    "* Studies have shown that individuals with excess Adipose tissue (AT) in the abdominal region have a higher risk of cardio-vascular diseases\n",
    "\n",
    "* Computed Tomography, commonly called the CT Scan is the only technique that allows for the precise and reliable measurement of the AT (at any site in the body)\n",
    "\n",
    "* The problems with using the CT scan are:\n",
    "    * Many physicians do not have access to this technology\n",
    "    * Irradiation of the patient (suppresses the immune system)\n",
    "    * Expensive\n",
    "    \n",
    "* Is there a simpler yet reasonably accurate way to predict the AT area? i.e.\n",
    "    * Easily available\n",
    "    * Risk free\n",
    "    * Inexpensive\n",
    "    \n",
    "* A group of researchers  conducted a study with the aim of predicting abdominal AT area using simple anthropometric measurements i.e. measurements on the human body\n",
    "\n",
    "* The Waist Circumference – Adipose Tissue data is a part of this study wherein the aim is to study how well waist circumference(WC) predicts the AT area"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 148,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Number of rows:  109 \n"
     ]
    },
    {
     "data": {
      "text/html": [
       "<table>\n",
       "<thead><tr><th scope=col>Waist</th><th scope=col>AT</th></tr></thead>\n",
       "<tbody>\n",
       "\t<tr><td>74.75</td><td>25.72</td></tr>\n",
       "\t<tr><td>72.60</td><td>25.89</td></tr>\n",
       "\t<tr><td>81.80</td><td>42.60</td></tr>\n",
       "\t<tr><td>83.95</td><td>42.80</td></tr>\n",
       "\t<tr><td>74.65</td><td>29.84</td></tr>\n",
       "\t<tr><td>71.85</td><td>21.68</td></tr>\n",
       "</tbody>\n",
       "</table>\n"
      ],
      "text/latex": [
       "\\begin{tabular}{r|ll}\n",
       " Waist & AT\\\\\n",
       "\\hline\n",
       "\t 74.75 & 25.72\\\\\n",
       "\t 72.60 & 25.89\\\\\n",
       "\t 81.80 & 42.60\\\\\n",
       "\t 83.95 & 42.80\\\\\n",
       "\t 74.65 & 29.84\\\\\n",
       "\t 71.85 & 21.68\\\\\n",
       "\\end{tabular}\n"
      ],
      "text/markdown": [
       "\n",
       "Waist | AT | \n",
       "|---|---|---|---|---|---|\n",
       "| 74.75 | 25.72 | \n",
       "| 72.60 | 25.89 | \n",
       "| 81.80 | 42.60 | \n",
       "| 83.95 | 42.80 | \n",
       "| 74.65 | 29.84 | \n",
       "| 71.85 | 21.68 | \n",
       "\n",
       "\n"
      ],
      "text/plain": [
       "  Waist AT   \n",
       "1 74.75 25.72\n",
       "2 72.60 25.89\n",
       "3 81.80 42.60\n",
       "4 83.95 42.80\n",
       "5 74.65 29.84\n",
       "6 71.85 21.68"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "# Setting working directory\n",
    "filepath <- c(\"/Users/nkaveti/Documents/Work_Material/Statistics Learning/\")\n",
    "setwd(filepath)\n",
    "\n",
    "# Reading data\n",
    "Waist_AT <- read.csv(\"adipose_tissue.csv\")\n",
    "cat(\"Number of rows: \", nrow(Waist_AT), \"\\n\")\n",
    "head(Waist_AT)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Let's start with a scatter plot of **Waist** Vs **AT**, to understand the relationship between these two variables."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "plot without title"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "plot(AT ~ Waist, data = Waist_AT)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Any observations from above plot?\n",
    "\n",
    "Now the objective is to find a linear relation between `Waist` and `AT`. In otherwords, finding the amount of change in `AT` per one unit change (increment/decrement) in `Waist`. \n",
    "\n",
    "In SLR, it is equivalent to finding an optimal straight line equation such that the sum of squares of differences between straight line and the points will be minimum. Hence, here we have two parameters for straight line, `intercept` and `slope`\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$AT  = \\beta_0 + \\beta_1 \\ Waist + \\epsilon$$\n",
    "\n",
    "$$Min_{\\beta_0 , \\beta_1} \\ \\ \\epsilon^\\intercal \\epsilon \\implies Min_{\\beta_0 , \\beta_1} \\ \\ (AT  - \\beta_0 + \\beta_1 \\ Waist)^\\intercal (AT  - \\beta_0 + \\beta_1 \\ Waist)$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now our problem becomes an unconstrained optimization problem. We can find optimal values for $\\beta_0$ and $\\beta_1$ using basic calculus.\n",
    "\n",
    "Lets re-write above regression equation in matrix form\n",
    "\n",
    "$$ AT = X \\beta + \\epsilon$$\n",
    "\n",
    "Where, $ X = [1 \\ \\ Waist]$ 1 is a vector of ones and $\\beta = (\\beta_0, \\ \\beta_1)$ "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$\n",
    "\\begin{equation}\n",
    "\\begin{split}\n",
    "\\epsilon^\\intercal \\epsilon & = {(AT - X \\beta)}^\\intercal {(AT - X \\beta)} \\\\\n",
    "& = AT^\\intercal AT - AT^\\intercal X \\beta - {(X \\beta)}^\\intercal AT + {(X \\beta)}^\\intercal (X \\beta)\n",
    "\\end{split}\n",
    "\\end{equation}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now differentiate this w.r.t to $\\beta$ and equate it to zero. Then we have,\n",
    "$$\\hat{\\beta} = (X^\\intercal X)^{-1} X^\\intercal AT $$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now we can find the fitted values of model by substituting $\\hat{\\beta}$ in above regression equation\n",
    "$$\\hat{AT} = X \\hat{\\beta}=X(X^\\intercal X)^{-1} X^\\intercal AT$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**Note:** We are arriving to above equation through an assumption$^1$ of $E(\\epsilon)=0$. What happens if this assumption violates?\n",
    "\n",
    "Let, $X(X^\\intercal X)^{-1} X^\\intercal = H$\n",
    "$$\\hat{AT} = H \\ AT$$\n",
    "\n",
    "We call H as an hat matrix, because it transforms $AT$ into $\\hat{AT}$ :D"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 99,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Let's compare the computed values with lm() output: \n",
      " \n",
      "Computed Coefficients: \n",
      " \n",
      "  Intercept    Waist\n",
      "1 -215.9815 3.458859\n",
      "======================================================================= \n",
      "\n",
      "Call:\n",
      "lm(formula = AT ~ Waist, data = Waist_AT)\n",
      "\n",
      "Coefficients:\n",
      "(Intercept)        Waist  \n",
      "   -215.981        3.459  \n",
      "\n",
      "======================================================================= \n"
     ]
    }
   ],
   "source": [
    "# Lets compute the hat matrix\n",
    "X = cbind(1, Waist_AT$Waist)\n",
    "temp = solve(t(X) %*% X) %*% t(X)\n",
    "betahat = temp %*% Waist_AT$AT # Estimated coefficients\n",
    "cat(\"Let's compare the computed values with lm() output: \\n \\n\")\n",
    "cat(\"Computed Coefficients: \\n \\n\")\n",
    "print(data.frame(Intercept = betahat[1], Waist = betahat[2]))\n",
    "cat(\"======================================================================= \\n\")\n",
    "#cat(\"Optimal value for beta_0 is: \", betahat[1], \"and for beta_1 is: \", betahat[2], \"\\n \\n\")\n",
    "fit_lm = lm(AT ~ Waist, data = Waist_AT)\n",
    "#cat(\"Compare our computed estimates with lm() estimates\", \"\\n\")\n",
    "print(fit_lm)\n",
    "cat(\"======================================================================= \\n\")\n",
    "H = X %*% temp # Computing hat matrix\n",
    "AThat = H %*% Waist_AT$AT # Computing predicted values"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## What's next?\n",
    "\n",
    "We succesfully computed estimates for regression coefficients and fitted values.\n",
    "\n",
    "1. We are working on only one sample, how can we generalise these results to population? \n",
    "\n",
    "2. How to measure model's performance quantitatively?\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**  We are working on only one sample, how can we generalise these results to population? **\n",
    "\n",
    "Let's focus on question 1. Our regression coefficients are computed using only one sample and these values will change, if we change the sample. But how much they vary? We need to estimate the variation for each beta coefficient to check whether the corresponding regressor is consistently explaining the same behaviour even if we change the sample.\n",
    "\n",
    "Now the big problem is collecting multiple samples to check the above hypothesis. Hence, we use distributions to check statistical significance of regressors.\n",
    "\n",
    "For our example, we need to test below two hypotheses. \n",
    "\n",
    "$$ Null \\ Hypothesis: \\beta_{0} = 0 $$\n",
    "\n",
    "$$ Alternative \\ Hypothesis: \\beta_{0} \\neq 0$$\n",
    "\n",
    "\n",
    "$$ Null \\ Hypothesis: \\beta_{1} = 0 $$\n",
    "\n",
    "$$ Alternative \\ Hypothesis: \\beta_{1} \\neq 0$$\n",
    "\n",
    "\n",
    "Test Statistic for these hypotheses is, \n",
    "\n",
    "$$t = \\frac{\\hat{\\beta_{i}}}{\\sqrt{Var(\\hat{\\beta_{i}})}}$$\n",
    "\n",
    "Test statistic `t` follows `t-distribution`, assuming$^2$ dependent variable follows `normal distribution`\n",
    "\n",
    "**Suggestion:** If your not aware of testing of hypothesis, distributions and p-values please browse through the Google.\n",
    "\n",
    "Let's recall that,  $\\hat{\\beta} = (X^\\intercal X)^{-1} X^\\intercal AT$\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "collapsed": true
   },
   "source": [
    "$$\\begin{equation}\n",
    "\\begin{split}\n",
    "Var(\\hat{\\beta}) & = Var((X^\\intercal X)^{-1} X^\\intercal AT) \\\\\n",
    " & = (X^\\intercal X)^{-1} X^\\intercal \\ Var(AT) \\ X(X^\\intercal X)^{-1} \\\\\n",
    " & = (X^\\intercal X)^{-1} X^\\intercal \\ X(X^\\intercal X)^{-1} \\ \\sigma^2 \\\\\n",
    " & = (X^\\intercal X)^{-1} \\sigma^2\n",
    "\\end{split}\n",
    "\\end{equation}\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**Note:** In the above calculations we assumed$^3$ $Var(AT) = \\sigma^2$ (Constant). Where, $\\sigma^2$ is variation in population AT."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "collapsed": true
   },
   "source": [
    "**Suggestion:** Try solving $(X^\\intercal X)^{-1}$ with $X = [1, \\  x]$ where $x = (x_1, x_2, x_3 ... x_n)$. You will get the following expression."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$\n",
    "\\\n",
    "Var(\\hat{\\beta}) = \n",
    "\\frac{1}{n \\sum x_i^2 - (\\sum x_i)^2}\n",
    "\\begin{bmatrix}\n",
    "    \\sum_{i=1}^n x_i^2 & -\\sum x_i \\\\\n",
    "    -\\sum x_i & n\n",
    "\\end{bmatrix}\n",
    "\\sigma^2\n",
    "\\\n",
    "$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "collapsed": true
   },
   "source": [
    "Diagonal elements of above matrix are varinaces of $\\beta_0$ and $\\beta_1$ respectively. Off-diagonal element is covariance between $\\beta_0$ and $\\beta_1$.\n",
    "\n",
    "Hence, \n",
    "\n",
    "$$Var(\\hat{\\beta_0}) = \\frac{\\sigma^2 \\sum_{i = 1}^n x_i^2}{n \\sum_{i = 1}^n (x_i - \\bar{x})^2}$$\n",
    "\n",
    "$$Var(\\hat{\\beta_1}) = \\frac{\\sigma^2}{\\sum_{i = 1}^n (x_i - \\bar{x})^2}$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "One important observation from $Var(\\hat{\\beta})$ expressions is, $Var(x)$ is inversely proportional to $Var(\\hat{\\beta})$. That is, we will get more consistent estimators if there is high variation in corresponding predictors.\n",
    "\n",
    "Recall that, $\\sigma^2$ in above expression is the population variance, not the sample. Hence, we need to estimate this using the sample that we have.\n",
    "\n",
    "$$\\hat{\\sigma^2} = \\frac{1}{n-2} \\sum_{i = 1}^n e_i^2$$\n",
    "\n",
    "Where, $e_i = AT_i - \\hat{AT}_i$"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 81,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Let's compare the computed values with lm() output: \n",
      " \n",
      "======================================================================= \n",
      "Computed Coefficients: \n",
      " \n",
      "               Estimate  Std.Error   t_value      p_value\n",
      "(Intercept) -215.981488 21.7962708 -9.909103 7.507198e-17\n",
      "Waist          3.458859  0.2346521 14.740376 1.297124e-27\n",
      "======================================================================="
     ]
    },
    {
     "data": {
      "text/plain": [
       "\n",
       "Call:\n",
       "lm(formula = AT ~ Waist, data = Waist_AT)\n",
       "\n",
       "Residuals:\n",
       "     Min       1Q   Median       3Q      Max \n",
       "-107.288  -19.143   -2.939   16.376   90.342 \n",
       "\n",
       "Coefficients:\n",
       "             Estimate Std. Error t value Pr(>|t|)    \n",
       "(Intercept) -215.9815    21.7963  -9.909   <2e-16 ***\n",
       "Waist          3.4589     0.2347  14.740   <2e-16 ***\n",
       "---\n",
       "Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1\n",
       "\n",
       "Residual standard error: 33.06 on 107 degrees of freedom\n",
       "Multiple R-squared:   0.67,\tAdjusted R-squared:  0.667 \n",
       "F-statistic: 217.3 on 1 and 107 DF,  p-value: < 2.2e-16\n"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "======================================================================="
     ]
    }
   ],
   "source": [
    "# Let's compute variances of beta hat and test statistic 't'\n",
    "sigmasq = (1/length(AThat[-c(1:2)]))*sum((AThat - Waist_AT$AT)^2)\n",
    "VarBeta0 = (sigmasq * sum(Waist_AT$Waist^2))/(length(AThat) * sum((Waist_AT$Waist - mean(Waist_AT$Waist))^2))\n",
    "VarBeta1 = sigmasq/sum((Waist_AT$Waist - mean(Waist_AT$Waist))^2)\n",
    "cat(\"Let's compare the computed values with lm() output: \\n \\n\")\n",
    "cat(\"======================================================================= \\n\")\n",
    "cat(\"Computed Coefficients: \\n \\n\")\n",
    "res = data.frame(Estimate = betahat, Std.Error = c(sqrt(VarBeta0), sqrt(VarBeta1)), t_value = c(betahat[1]/sqrt(VarBeta0), betahat[2]/sqrt(VarBeta1)))\n",
    "row.names(res) = c(\"(Intercept)\", \"Waist\")\n",
    "res$p_value = 2*pt(abs(res$t_value), nrow(Waist_AT)-1, lower.tail = FALSE)\n",
    "print(res)\n",
    "cat(\"=======================================================================\")\n",
    "summary(fit_lm)\n",
    "cat(\"=======================================================================\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**Note:** Residual standard error = $\\sqrt{sigmasq}$\n",
    "\n",
    "**How to measure model's performance quantitatively?**\n",
    "\n",
    "Let's focus on question 2 (How to measure model's performance quantitatively?). Recall that, our objective of building model is to explain the variation in `AT` using the variation in `Waist`. \n",
    "\n",
    "Total variation in AT is, $\\sum_{i=1}^n (AT - mean(AT))^2$ this can be splitted into two parts as follows:\n",
    "\n",
    "$$\n",
    "\\begin{equation}\n",
    "\\begin{split}\n",
    "\\sum_{i=1}^n (AT_i - \\bar{AT})^2 & = \\sum_{i=1}^n (AT  - \\hat{AT_i} + \\hat{AT_i} - \\bar{AT})^2 \\\\\n",
    "& = \\sum_{i = 1}^n (\\hat{AT_i} - \\bar{AT})^2 + \\sum_{i=1}^n (AT_i - \\hat{AT_i})^2 \n",
    "\\end{split}\n",
    "\\end{equation}\n",
    "$$\n",
    "\n",
    "Where, $\\sum_{i=1}^n (AT_i - \\bar{AT})^2$ is the total variation in AT, $\\sum_{i = 1}^n (\\hat{AT_i} - \\bar{AT})^2$ is the explained variation in AT, this is also called as **Regression Sum of Squares** and $\\sum_{i=1}^n (AT_i - \\hat{AT_i})^2$ is the unexplained variation in AT, this is also called as **Error Sum of Squares**\n",
    "\n",
    "We can measure our model using the proportion of total variation explained by independent variable(s). That is, $\\frac{Regression \\  Sum \\ of \\ Squares}{Total \\ Sum \\ of \\ Squares}$\n",
    "\n",
    "The above measure is called as Multiple R-squared:\n",
    "\n",
    "$$Multiple \\ R-squared = \\frac{\\sum_{i = 1}^n (\\hat{AT_i} - \\bar{AT})^2}{\\sum_{i=1}^n (AT_i - \\bar{AT})^2}$$"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 143,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Compute Multiple R-squared:  0.6700369 \n",
      " \n",
      "Note that computed R squared value is matching with lm() Multiple R-squared value in above output \n",
      " \n",
      "======================================================================= \n",
      " \n"
     ]
    }
   ],
   "source": [
    "# Let's compute Multiple R-squared measure for our example\n",
    "SSR = sum((AThat - mean(Waist_AT$AT))^2)\n",
    "SST = sum((Waist_AT$AT - mean(Waist_AT$AT))^2)\n",
    "MulRSq = SSR/SST\n",
    "cat(\"Compute Multiple R-squared: \", MulRSq, \"\\n \\n\")\n",
    "cat(\"Note that computed R squared value is matching with lm() Multiple R-squared value in above output \\n \\n\")\n",
    "cat(\"======================================================================= \\n \\n\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "collapsed": true
   },
   "source": [
    "**What happens to the Multiple R-squared value when you add an irrelevant variable to the model?**\n",
    "\n",
    "In the below model, I am generating a random sample of uniform numbers between 1 to 100 and considering this as one of indepedent variable."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 131,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "\n",
       "Call:\n",
       "lm(formula = AT ~ Waist + runif(nrow(Waist_AT), 1, 100), data = Waist_AT)\n",
       "\n",
       "Residuals:\n",
       "    Min      1Q  Median      3Q     Max \n",
       "-106.06  -17.53   -3.63   13.70   91.36 \n",
       "\n",
       "Coefficients:\n",
       "                               Estimate Std. Error t value Pr(>|t|)    \n",
       "(Intercept)                   -226.2894    23.4350  -9.656 3.33e-16 ***\n",
       "Waist                            3.5060     0.2376  14.757  < 2e-16 ***\n",
       "runif(nrow(Waist_AT), 1, 100)    0.1397     0.1181   1.183    0.239    \n",
       "---\n",
       "Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1\n",
       "\n",
       "Residual standard error: 33 on 106 degrees of freedom\n",
       "Multiple R-squared:  0.6743,\tAdjusted R-squared:  0.6682 \n",
       "F-statistic: 109.7 on 2 and 106 DF,  p-value: < 2.2e-16\n"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "======================================================================= \n",
      " \n"
     ]
    }
   ],
   "source": [
    "set.seed(1234)\n",
    "fit_lm2 = lm(AT ~ Waist + runif(nrow(Waist_AT), 1, 100), data = Waist_AT)\n",
    "summary(fit_lm2)\n",
    "cat(\"======================================================================= \\n \\n\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Multiple R-squared value increases irrespective of quality of explanation, which is incorrect. We should penalize our model performance if the quality of explanation is poor, that is why we need to adjust our R-squared value.\n",
    "\n",
    "To penalize the explained part of AT, we inflate the unexplained part of AT with $\\frac{Total \\ degrees \\ of \\ freedom}{Error \\ degrees \\ of \\ freedom}$. That is,\n",
    "\n",
    "$$Adjusted \\ R-squared = 1 - (1 - R^2) \\frac{n-1}{n-p-1}$$\n",
    "\n",
    "Where, n = Total number of observations; p = Total number of predictors (excluding intercept)\n",
    "\n",
    "Adding a new independent variable will increase $\\frac{n-1}{n-p-1}$ and $R^2$. If the amount of increment in $R^2$ is less than the amount of increment in $\\frac{n-1}{n-p-1}$ than it will decrease the Adjusted R-squared value.\n",
    "\n",
    "In `fit_lm2` model Adjusted R-squared decreases when we add randomly generated variable into the model. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 142,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Compute Multiple R-squared:  0.6669531 \n",
      " \n",
      "Note that computed Adjusted R-squared value is matching with lm() Adjusted R-squared value in the above output \n",
      " \n",
      "Note: We are comparing with fit_lm model, not fit_lm2 \n",
      "======================================================================= \n"
     ]
    }
   ],
   "source": [
    "# Let's compute adjusted R-squared  for our example\n",
    "TDF = nrow(Waist_AT[-1, ]) # Total degrees of freedom\n",
    "EDF = nrow(Waist_AT[-1, ]) - 1 # Error degrees of freedom, where 1 is the number of predictors\n",
    "AdjRSq = 1 - (1 - MulRSq) * (TDF/EDF) # Adjusted R square\n",
    "cat(\"Compute Multiple R-squared: \", AdjRSq, \"\\n \\n\")\n",
    "cat(\"Note that computed Adjusted R-squared value is matching with lm() Adjusted R-squared value in the above output \\n \\n\")\n",
    "cat(\"Note: We are comparing with fit_lm model, not fit_lm2 \\n\")\n",
    "cat(\"======================================================================= \\n\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Aforementioned measures (Multiple R-squared & Adjusted R-squared) for **Goodness of fit** are functions of sample and these will vary as sample changes. Similar to `t-test` for regression coefficeints we need some statistical test to test model's performance for population.\n",
    "\n",
    "Objective is to compare the Mean sum of squares due to regression and Mean sum of squares due to error. `F-test` is very helpful to compare the variations.\n",
    "\n",
    "$$ F-test = \\frac{\\frac{1}{p-1}\\sum_{i=1}^n (\\hat{AT_i} - \\bar{AT})^2}{\\frac{1}{n-p-1} \\sum_{i=1}^n (\\hat{AT_i} - AT_i)^2}$$\n",
    "\n",
    "**Note:** Above expression follows F distribution only if, AT follows Normal Distribution"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 145,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Compute F statistic:  217.2787 \n",
      " \n",
      "Note that computed F-statistic is matching with lm() F-statistic value in the above output \n",
      " \n",
      "Note: We are comparing with fit_lm model, not fit_lm2 \n",
      "======================================================================= \n"
     ]
    }
   ],
   "source": [
    "RDF = TDF - EDF\n",
    "SSE = SST - SSR\n",
    "MSR = (1/RDF)*SSR\n",
    "MSE = (1/EDF)*SSE\n",
    "F_value = MSR/MSE\n",
    "cat(\"Compute F statistic: \", F_value, \"\\n \\n\")\n",
    "cat(\"Note that computed F-statistic is matching with lm() F-statistic value in the above output \\n \\n\")\n",
    "cat(\"Note: We are comparing with fit_lm model, not fit_lm2 \\n\")\n",
    "cat(\"======================================================================= \\n\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "R",
   "language": "R",
   "name": "ir"
  },
  "language_info": {
   "codemirror_mode": "r",
   "file_extension": ".r",
   "mimetype": "text/x-r-source",
   "name": "R",
   "pygments_lexer": "r",
   "version": "3.3.2"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 0
}
