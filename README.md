# Education-report
SQL data extraction




--Primary school SQL scripts


DECLARE @PublishEndDate date;
SET @PublishEndDate = ''
SET NOCOUNT ON;
SELECT cast(specimenprocesseddate as date) as specimenprocesseddate,
 age_in_years,
organisationrole,
 lfd_method,
 sum(positives) as positives,
 sum(negatives) as negatives,
 sum(voids) as voids
 FROM (
                    SELECT *
					FROM (
                    SELECT
                    ern.specimenid,
		    case when lower(administrationmethod) = 'self' then 'selfserve' else 'supervised' end as lfd_method,
                    ern.testid,
                    ern.testcentreid,
                    ern.age_in_years,
                    ern.createsource,
                    ern.specimenprocesseddate,
		    RecordCreatedDate,
                    ern.organisationrole,
                    ern.organisationid,
		    ern.processinglabcode,
                    ern.testresult,
                    case when ern.testresult = 'positive' then 1 else 0 end as positives,
                    case when ern.testresult = 'negative' then 1 else 0 end as negatives,
                    case when ern.testresult = 'void' or ern.testresult = 'unknown/void' then 1 else 0 end as voids  
                    FROM   ern
                    WHERE lower(ern.testcentreid) in ('shl','oth') 
					and lower(ern.createsource) ='liteself'
					AND ern.edge_specimen_rank = 1
					AND upper(countrycode) like 'E%'
                    AND cast(ern.specimenprocesseddate as date) >= ''
                    AND cast(ern.specimenprocesseddate as date) <= @PublishEndDate
                    AND cast(ern.organisationid as varchar) in (select distinct cast(uon as varchar) as uon from user_1  where coalesce(lower(use_case_type),'') = 'school_or_college' and (coalesce(lower(school_breakdown),'') = 'primary' or UPPER(coalesce(school_breakdown,'NULL')) = 'NULL'))
                    AND ern.organisationid is not null
                    UNION ALL 
                    SELECT 
                    ern.specimenid,
		    case when lower(administrationmethod) = 'self' then 'selfserve' else 'supervised' end as lfd_method,
                    ern.testid,
                    ern.testcentreid,
                    ern.age_in_years,
                    ern.createsource,
                     ern.specimenprocesseddate,
		    RecordCreatedDate,
                    ern.organisationrole,
                    ern.organisationid,
		   ern.processinglabcode,
                    ern.testresult,
                    case when ern.testresult = 'positive' then 1 else 0 end as positives,
                    case when ern.testresult = 'negative' then 1 else 0 end as negatives,
                    case when ern.testresult = 'void' or ern.testresult = 'unknown/void' then 1 else 0 end as voids  
                    FROM   ern
                    WHERE  (lower(ern.testcentreid) = 'shl' or lower(ern.testcentreid) = 'oth') and
                    lower(ern.createsource) ='liteself'
					AND ern.edge_specimen_rank = 1
					AND upper(countrycode) like 'E%'
                    AND cast(ern.specimenprocesseddate as date) >= ''
                    AND cast(ern.specimenprocesseddate as date)  < ''
                    AND ern.organisationid is null
                    UNION ALL
                    SELECT 
                    ern.specimenid,
		  case when lower(administrationmethod) = 'self' then 'selfserve' else 'supervised' end as lfd_method,
                    ern.testid,
                    ern.testcentreid,
                    ern.age_in_years,
                    ern.createsource,
                    ern.specimenprocesseddate,
		    RecordCreatedDate,
                    ern.organisationrole,
                    ern.organisationid,
		     ern.processinglabcode,
                    ern.testresult,
                    case when ern.testresult = 'positive' then 1 else 0 end as positives,
                    case when ern.testresult = 'negative' then 1 else 0 end as negatives,
                    case when ern.testresult = 'void' or ern.testresult = 'unknown/void' then 1 else 0 end as voids  
                    FROM   ern
                    WHERE  (lower(ern.testcentreid) = 'shl' or lower(ern.testcentreid) = 'oth') and lower(ern.createsource) ='liteself'
					AND ern.organisationid is not null
					AND ern.edge_specimen_rank = 1
					AND upper(countrycode) like 'E%'
                    AND cast(ern.specimenprocesseddate as date) >= ''
                    AND cast(ern.specimenprocesseddate as date)  < ''
                    AND cast(coalesce(ern.organisationid,'') as varchar) not in (select distinct cast(uon as varchar) as uon from user_1  where coalesce(lower(use_case_type),'') = 'school_or_college' and UPPER(coalesce(uon,'NULL')) != 'NULL')
                    AND ern.organisationid is not null) as dl_a) as dl_b
					group by cast(specimenprocesseddate as date),
					age_in_years,
					organisationrole,
					lfd_method;


------ Secondary school SQL script

--Secondary
DECLARE @PublishEndDate date;
SET @PublishEndDate = ''
SET NOCOUNT ON;
   SELECT cast(specimenprocesseddate as date) as specimenprocesseddate,
	age_in_years,
	school_breakdown,
	organisationrole,
	lfd_method,     
	sum(positives) as positives,
	sum(negatives) as negatives,
	sum(voids) as voids
   FROM (
                    SELECT *
					FROM (
                    SELECT
                    ern.specimenid,
		   case when lower(administrationmethod) = 'self' then 'selfserve' else 'supervised' end as lfd_method,
                    ern.testid,
                    ern.testcentreid,
                    ern.age_in_years,
                    ern.createsource,
                    ern.specimenprocesseddate,
		    RecordCreatedDate,
                    ern.organisationrole,
                    ern.organisationid,
                    ra.use_case_type,
                    ra.school_breakdown,
                    ern.testresult,
                    case when ern.testresult = 'positive' then 1 else 0 end as positives,
                    case when ern.testresult = 'negative' then 1 else 0 end as negatives,
                    case when ern.testresult = 'void' or ern.testresult = 'unknown/void' then 1 else 0 end as voids                    
                    FROM  ern
                    LEFT JOIN user_2 ra on ern.testcentreid = ra.testcentreid
                    WHERE cast(ern.specimenprocesseddate as date) >= ''
                    AND   cast(ern.specimenprocesseddate as date) <= @PublishEndDate
					AND ern.edge_specimen_rank = 1
					AND upper(countrycode) like 'E%'
                    AND ern.testcentreid in (select distinct testcentreid from user_2 where coalesce(lower(use_case_type),'') = 'school_or_college' and (coalesce(lower(school_breakdown),'') in ('secondary', 'colleges') or UPPER(coalesce(school_breakdown,'NULL')) = 'NULL'))
                    AND  lower(testkit) = 'lft'
                    UNION ALL
                    SELECT
                    ern.specimenid,
		    case when lower(administrationmethod) = 'self' then 'selfserve' else 'supervised' end as lfd_method,
                    ern.testid,
                    ern.testcentreid,
                    ern.age_in_years,
                    ern.createsource,
                   ern.specimenprocesseddate,
		    RecordCreatedDate,
                    ern.organisationrole,
                    ern.organisationid,
                    ra.use_case_type,
                    ra.school_breakdown,                 
                    ern.testresult,
                    case when ern.testresult = 'positive' then 1 else 0 end as positives,
                    case when ern.testresult = 'negative' then 1 else 0 end as negatives,
                    case when ern.testresult = 'void' or ern.testresult = 'unknown/void' then 1 else 0 end as voids  
                    FROM  ern
                    LEFT JOIN user_1 ra on cast(ern.organisationid as varchar) = cast(ra.uon as varchar)
                    WHERE cast(ern.specimenprocesseddate as date) >= ''
                    AND   cast(ern.specimenprocesseddate as date) <= @PublishEndDate
					AND upper(countrycode) like 'E%'
                    AND    (lower(ern.testcentreid) = 'shl' or lower(ern.testcentreid) = 'oth') and lower(ern.createsource) = 'liteself'
                    AND ern.organisationid in (select distinct uon from  user_temp.tempref1 where coalesce(lower(use_case_type),'') = 'school_or_college' and coalesce(lower(school_breakdown),'') in ('secondary', 'colleges'))
                    AND ern.edge_specimen_rank = 1 
                    UNION ALL
                    SELECT
                    ern.specimenid,
		   case when lower(administrationmethod) = 'self' then 'selfserve' else 'supervised' end as lfd_method,
                    ern.testid,
                    ern.testcentreid,
                    ern.age_in_years,
                    ern.createsource,
                   ern.specimenprocesseddate,
		   RecordCreatedDate,
                    ern.organisationrole,
                    ern.organisationid,
                    ra.use_case_type,
                    ra.school_breakdown,                  
                    ern.testresult,
                    case when ern.testresult = 'positive' then 1 else 0 end as positives,
                    case when ern.testresult = 'negative' then 1 else 0 end as negatives,
                    case when ern.testresult = 'void' or ern.testresult = 'unknown/void' then 1 else 0 end as voids  
                    FROM   ern
                    LEFT JOIN user_1 ra on cast(ern.organisationid as varchar) = cast(ra.uon as varchar)
                    WHERE cast(ern.specimenprocesseddate as date) >= ''
                    AND  cast(ern.specimenprocesseddate as date) <= @PublishEndDate
					AND upper(countrycode) like 'E%'
					AND ern.edge_specimen_rank = 1
                    AND  lower(ern.testcentreid) = 'shl' and lower(ern.createsource) = 'bulk') as dl_a) as dl_b
					group by cast(specimenprocesseddate as date),
					age_in_years,
					school_breakdown,
					organisationrole,
					lfd_method ;


------------------------------------------------------------------------------------------------------------------------------------------
