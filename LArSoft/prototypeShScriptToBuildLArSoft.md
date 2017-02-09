#Prototype .sh script to build LArSoft

Prototype script for building LArSoft with specific products (e.g. in this case `lardataobj`)

```
run(){
  COLOR='\033[1;33m'
  DEFAULT='\033[0m'
  echo -e "${COLOR}-> ${1}${DEFAULT}";
  eval ${1};
}

V_BAS='larsoft'
V_PROJ1='uboonecode'
V_PROJ2='lardataobj'
V_VERS1='v06_23_00'
V_VERS2='v1_10_00'
V_QUAL1='e10'
V_QUAL2='prof'
CURR="${PWD}"

run "rm -rf ${V_BAS}_${V_VERS1}";
run "rm source_${V_BAS}_${V_VERS1}.sh";

run "source /grid/fermiapp/products/uboone/setup_uboone.sh";
run "setup git";
run "setup gitflow";
run "setup mrb";
run "export MRB_PROJECT=larsoft";
run "setup ${V_BAS} ${V_VERS1} -q ${V_QUAL1}:${V_QUAL2}";
run "mkdir ${V_BAS}_${V_VERS1}";
run "cd ${V_BAS}_${V_VERS1}";
run "mrb newDev";
run "source localProducts_${V_BAS}_${V_VERS1}_${V_QUAL1}_${V_QUAL2}/setup";
run "cd ${MRB_SOURCE}";
run "mrb g -t ${V_VERS1} ${V_PROJ1}";
run "mrb g -t ${V_VERS2} ${V_PROJ2}";
run "mrb g ${V_PROJ3}";
run "cd ${MRB_BUILDDIR}";
run "mrbsetenv";
run "mrb i -j8";
run "cd ${MRB_TOP}";
run "mrbslp";

run "cd ${CURR}";
run "touch source_${V_BAS}_${V_VERS1}.sh";
echo 'run(){' >> source_${V_BAS}_${V_VERS1}.sh;
echo '  COLOR="\033[1;33m"' >> source_${V_BAS}_${V_VERS1}.sh;
echo '  COLOR="\033[1;33m"' >> source_${V_BAS}_${V_VERS1}.sh;
echo '  DEFAULT="\033[0m"' >> source_${V_BAS}_${V_VERS1V_VERS1}}.sh;
echo '  echo -e "${COLOR}-> ${1}${DEFAULT}";' >> source_${V_BAS}_${V_VERS1}.sh;
echo '  eval ${1};' >> source_${V_BAS}_${V_VERS1}.sh;
echo '}' >> source_${V_BAS}_${V_VERS1}.sh;
echo "run 'source /grid/fermiapp/products/uboone/setup_uboone.sh'" >> source_${V_BAS}_${V_VERS1}.sh;
echo "run 'setup ${V_BAS} ${V_VERS1} -q ${V_QUAL1}:${V_QUAL2}'" >> source_${V_BAS}_${V_VERS1}.sh;
echo "run 'source ${V_BAS}_${V_VERS1}/localProducts_${V_BAS}_${V_VERS1}_${V_QUAL1}_${V_QUAL2}/setup'" >> source_${V_BAS}_${V_VERS1}.sh;
echo "run 'mrbslp'" >> source_${V_BAS}_${V_VERS1}.sh;
echo "run 'mrbsetenv'" >> source_${V_BAS}_${V_VERS1}.sh;

```

To know which tagged version to use for your secondary products look at the guide on fixing version conflicts.

After that, when you login, all you need to do is to source the soruce file created by the script:

```
source source_xxx_vyy_yy_yy.sh
```