cd "/Users/mmorfin/Documents/IPO/Data"

*-------------------------------------------------------------------------------
* Compustat Cleaning
*-------------------------------------------------------------------------------
{
clear
use "compustat_full.dta"
keep gvkey fyear ein conm
rename gvkey GVKEY
rename fyear YEAR
helloe wndkjsndjknfkjnds
rename conm name
destring GVKEY, replace
save "compustat_ein.dta", replace
}
*-------------------------------------------------------------------------------
* DualClass Clean and Merge
*-------------------------------------------------------------------------------
clear
import excel "DualClass.xls", firstrow
{
keep NAME GVKEY YEAR
destring GVKEY,replace
drop if missing(GVKEY) //drop observations missing a gvkey identifier (5 obs)


** Check for more than one observation per year
bysort GVKEY YEAR: gen check = _N
tab check
drop if NAME == "EVERGREEN MEDIA" // only 1 company had dupe. kept 1 observation
drop check
summarize

merge 1:m GVKEY YEAR using "compustat_ein.dta"

*** Temporarily keeping _merge == 3 to check for changes of EIN over time 
// keep if _merge == 3
//
// sort GVKEY YEAR
// by GVKEY: gen check = 1 if ein[_n] != ein[_n-1] & ein[_n-1] != ""
// tab check 
*** EIN remains same for all years

drop if _merge == 2

** Check for multiple observations per year
sort GVKEY YEAR
by GVKEY: gen check = 1 if YEAR[_n] == YEAR[_n-1]
drop if check == 1
drop check

/* _merge == 1 are firms in the dual class dataset that didn't merge with compustat. 
After reviewing them I noticed most are missing years for companies that successfully
merged for other years. This might be due to the YEAR variable used in the dual class 
dataset not being exactly aligned with fiscal year. Knowing GVKEY or EIN would 
not change over time(I checked above), I assinged missing values the previous 
years information. */

** Updates data for observations that were picked up but not 
gsort GVKEY -name 
by GVKEY: replace ein = ein[_n-1] if missing(ein) & _merge == 1
by GVKEY: replace name = name[_n-1] if missing(name) & _merge == 1
sort GVKEY YEAR
}

*-------------------------------------------------------------------------------
* Manually Identifying
*-------------------------------------------------------------------------------
{
/* Instead of droping the remaining companies observation that were not 
matched(48 obs; 16 individual companies) I went back into conpustat(WRDS) and 
searched for only those gvkeys. When looking at these missing observations they 
didnt match due to year and fyear not matching up. I decided to manually update
them since only a handful actaully had an EIN. There was 1 
company (11442; WESTON ROY F INC ) where the GVKey was not found on compustat*/

replace name = "AMERICONNECT INC" if GVKEY == 15849
replace ein = "48-1056927" if GVKEY == 15849

replace name = "QUAL-MED INC/DE" if GVKEY == 24166
replace ein = "84-1175468" if GVKEY == 24166

replace name = "GREENWICH AIR SERVICES -CL A" if GVKEY == 29132
replace ein = "58-1758941" if GVKEY == 29132

replace name = "USA MOBILE COMM HLDGS  -CL A" if GVKEY == 29671
replace ein = "31-1358569" if GVKEY == 29671

replace name = "FIRSTCOM CORP" if GVKEY == 30750
replace ein = "87-0464860" if GVKEY == 30750

replace name = "MOBILEMEDIA CORP  -CL A" if GVKEY == 60959
replace ein = "22-3253006" if GVKEY == 60959

replace name = "PETERSEN COMPANIES  -CL A" if GVKEY == 65575
replace ein = "36-4099296" if GVKEY == 65575

replace name = "CAPSTAR BROADCASTING CORP" if GVKEY == 110960
replace ein = "74-2833106" if GVKEY == 110960

replace name = "CLEVELAND INDIANS BASEBALL" if GVKEY == 111202
replace ein = "34-1861303" if GVKEY == 111202

replace name = "PCORDER.COM INC" if GVKEY == 118579
replace ein = "74-2720849" if GVKEY == 118579

replace name = "RESOURCEPHOENIX.COM INC" if GVKEY == 124681
replace ein = "68-0393895" if GVKEY == 124681

replace name = "TRITEL INC" if GVKEY == 127399
replace ein = "64-0896417" if GVKEY == 127399

drop _merge

replace ein = "." if ein == ""
}

** Save
save "gvkey_ein_dualclass.dta", replace
export delimited gvkey_ein_dualclass.csv, replace
