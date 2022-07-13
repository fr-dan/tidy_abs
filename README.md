# tidy_abs
Tidy dataset formats of the Office for National Statistics' Annual Business Survey


## Code for replication

```{r}

pacman::p_load(openxlsx, janitor, here, magrittr, dplyr, usethis)

use_data_raw() # Save workbook of Annual Business Survey to here

sheet_list = list()

for (x in 1:20){
  sheet_list[[x]] = read.xlsx(here("data-raw", "abssectionsas.xlsx"),
                              sheet = x + 4,
                              colNames = T,
                              startRow = 7)
}


abs = bind_rows(sheet_list) %>%
  clean_names() %>%
  rename(sic = 1,
         gva = 6,
         purchases = 7,
         employment_costs = 8,
         acquisitions = 9,
         disposals = 10,
         end_year_value = 11,
         beginning_year_value = 12,
         during_year_value = 13,
         increase_during_year = 14,
         sic_division = 15) %>%
  select(-sic_division) %>%
  mutate_at(vars(c(1, 3:14)), "as.numeric") %>%
  drop_na(sic)
  
# Pivot to long form

abs %<>% pivot_longer(cols = c(4:14), names_to = "unit", values_to = "value")


abs %>% write_csv(here("data", "abs_tidy_long.csv"))


```
