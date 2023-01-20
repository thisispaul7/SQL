-- Imported CovidDeaths and CovidVaccinations into SQL Server
-- Create Query

-- View table, ordered by location and date
--select * from CovidDeaths$
--order by 3,4

--select * from CovidVaccinations$
--order by 3,4

-- Looking at both tables to see data types

select TABLE_NAME, COLUMN_NAME, DATA_TYPE from INFORMATION_SCHEMA.COLUMNS


-- Select Data being used

Select Location, date, total_cases, new_cases, total_deaths, population
from [SQL-PaulPortfolio]..CovidDeaths$
order by 1,2

-- Looking at total cases vs total deaths
-- Shows the likelihood of dying if you contract Covid in your country

Select Location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
from CovidDeaths$
where location like '%states%'
order by 1,2

-- Looking at the total cases vs the population

Select Location, date, total_cases, population, round((total_cases/population)*100,0) as PercentageInfected
from CovidDeaths$
where location like '%states%'
order by 1,2

-- Looking at  Counrtries with Highest Infection Rate compared to Population

Select Location, population, MAX(total_cases) as HighestInfectionCount, round(MAX((total_cases/population))*100,1) as PercentageInfected
from CovidDeaths$
group by location, population
order by PercentageInfected desc

-- Showing Countries with Highest Death per Population

Select Location, MAX(cast(total_deaths as int)) as HighestDeathCount
from CovidDeaths$
where continent is not null
group by location
order by HighestDeathCount desc

-- Showing Continent with highest death count

Select continent, MAX(cast(total_deaths as int)) as TotalDeathCount
from [SQL-PaulPortfolio]..CovidDeaths$
where continent is not null
group by continent
order by TotalDeathCount desc

-- Global numbers

Select --date,
sum(new_cases) SumNewCases, 
sum(cast(new_deaths as int)) SumNewDeaths, 
round(sum(cast(new_deaths as int))/sum(new_cases)*100,2) DeathPercentage
from [SQL-PaulPortfolio]..CovidDeaths$
where continent is not null
--group by date
order by 1,2

-- Looking at total population vs vaccinations using a join

select 
dea.continent, 
dea.location, 
dea.date, 
dea.population, 
vac.new_vaccinations,
sum(convert(bigint,vac.new_vaccinations)) over (partition by dea.location order by dea.location, dea.date) RollingPeopleVac
from [SQL-PaulPortfolio]..CovidDeaths$ dea
join [SQL-PaulPortfolio]..CovidVaccinations$ vac
	on dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null
order by 2,3


-- Using a CTE

With PopvsVac (Continent, Location, Date, Population, new_vaccinations, RollingPeopleVac)
as
(
select 
dea.continent, 
dea.location, 
dea.date, 
dea.population, 
vac.new_vaccinations,
sum(convert(bigint,vac.new_vaccinations)) over (partition by dea.location order by dea.location, dea.date) RollingPeopleVac
from [SQL-PaulPortfolio]..CovidDeaths$ dea
join [SQL-PaulPortfolio]..CovidVaccinations$ vac
	on dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null
--order by 2,3
)
Select *, (RollingPeopleVac/Population)*100
from PopvsVac


-- Temp Table
Drop table if exists #PercentPopulationVaccinated  --drops table so you can run the code multiple times without error
Create Table #PercentPopulationVaccinated
(
Continent nvarchar (255),
Location nvarchar (255),
Date datetime,
Population numeric,
New_vaccinations numeric,
RollingPeopleVac numeric
)

Insert into #PercentPopulationVaccinated
select 
dea.continent, 
dea.location, 
dea.date, 
dea.population, 
vac.new_vaccinations,
sum(convert(bigint,vac.new_vaccinations)) over (partition by dea.location order by dea.location, dea.date) RollingPeopleVac
from [SQL-PaulPortfolio]..CovidDeaths$ dea
join [SQL-PaulPortfolio]..CovidVaccinations$ vac
	on dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null
order by 2,3

Select *, (RollingPeopleVac/Population)*100
from #PercentPopulationVaccinated


-- Creating view to store data for later visualizations

CREATE VIEW PercentPopulationVaccinated2 as
select 
dea.continent, 
dea.location, 
dea.date, 
dea.population, 
vac.new_vaccinations,
sum(convert(bigint,vac.new_vaccinations)) over (partition by dea.location order by dea.location, dea.date) RollingPeopleVac
from [SQL-PaulPortfolio]..CovidDeaths$ dea
join [SQL-PaulPortfolio]..CovidVaccinations$ vac
	on dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null
--order by 2,3

-- Created another view focusing on United States vaccinations

CREATE VIEW PercentPopulationVaccinated3 as
select 
dea.continent, 
dea.location, 
dea.date, 
dea.population, 
vac.new_vaccinations,
sum(convert(bigint,vac.new_vaccinations)) over (partition by dea.location order by dea.location, dea.date) RollingPeopleVac
from [SQL-PaulPortfolio]..CovidDeaths$ dea
join [SQL-PaulPortfolio]..CovidVaccinations$ vac
	on dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null
AND dea.location = 'United States'



