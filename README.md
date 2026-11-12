# NYC Charter Schools Map

An interactive map of all 286 charter schools operating in New York City, based on NYSED's October 2026 list.

**Live map:** https://osheenk1999.github.io/nyc-charter-schools-map/

## Overview

The map shows every NYC charter school on the state's official list. Success Academy is shown at the campus level, with all 59 of its campuses highlighted in orange. All other schools are shown at one address each, in blue. Users can filter by borough or network, search by name, address, or zip code, and click any location for details.

## Definitions

- **Charter school:** A school operating under its own charter, as listed by NYSED. This is how the state counts charter schools. As of October 2026, NYC has 286.
- **Campus:** A physical building where a school operates. One charter can run several campuses (for example, an elementary school and a middle school), and several charters can share one building.
- **Location:** A point on the map. Schools or campuses at the same address share one point, and the popup lists all of them.

| | What a dot represents | Count |
|---|---|---|
| Success Academy (orange) | A campus | 59 campuses, covering 38 charters |
| All other networks (blue) | A charter school, at its main address | 248 charter schools |

## Data Sources

| Data | Source | As of |
|---|---|---|
| List of charter schools | [NYSED, New York State Charter Schools](https://www.nysed.gov/charter-schools/facts-about-charter-schools-new-york-state) | October 20, 2026 |
| School locations, grades, enrollment | [NCES Public School Administrative Data API](https://nces.ed.gov/programs/edge/Geographic/SchoolLocations) | 2024-25 school year |
| Addresses for schools opened in 2025 and 2026 | [SUNY Charter Schools Institute](https://www.newyorkcharters.org), [NYSED](https://www.nysed.gov/charter-schools), [NYC Public Schools](https://www.schools.nyc.gov) | November 2026 |
| Geocoding for those addresses and Success Academy campuses | [NYC Planning Geosearch API](https://geosearch.planninglabs.nyc) | November 2026 |
| Success Academy campuses | [Success Academy, School Locations](https://www.successacademies.org/locations/) | November 2026 |
| Count check | [NYC Charter School Center](https://nyccharterschools.org/find-a-charter-school/) | 2025-26 |
| Basemap | [Esri World Light Gray Canvas](https://www.arcgis.com/home/item.html?id=979c6cc89af9449cbeb5342a439c6a76) | Live |

## Methodology

1. Filtered the NYSED list to schools in NYC community school districts marked as open with enrolled students. This produced 286 schools, the same total the NYC Charter School Center reported for 2025-26.
2. Matched each school to the NCES dataset using its BEDS code, the state's school ID. NCES stores this code as its state ID, which makes for an exact join.
3. For 8 schools where NCES lists the code under a different district prefix, matched on the last six digits instead, where that match was unique.
4. For the 10 schools that opened after the NCES data was published, found addresses from the authorizer and NYC DOE, then geocoded them with NYC Geosearch.
5. For Success Academy, replaced the 38 charter-level points with the 59 campuses listed on its website, geocoded with NYC Geosearch.
6. Grouped schools and campuses that share a building into a single map point, so everything at that address is visible in one popup.

When the page loads, it requests current location data from the NCES API for the non-Success Academy schools. If the request fails or takes more than 6 seconds, the page uses a saved copy of the same data, so the map always loads.

## Assumptions

- **"Charter school"** means a school on NYSED's list. This covers schools authorized by SUNY, the Board of Regents, and NYC DOE.
- **"Operating"** means NYSED marks the school as open with enrolled students. Schools that are approved but not yet open (8 in NYC, opening in 2027) are excluded.
- **"In NYC"** means the school sits in one of NYC's 32 community school districts.
- **Borough** is based on the school's zip code.
- **Network** labels are based on school names (for example, any school with "Success Academy" in its name). Schools not in one of the larger networks are grouped as "Other / independent."
- **Success Academy campuses** are taken as listed on Success Academy's website. I did not link each campus to a specific charter, since the website lists campuses by name and grade, not by charter.
- **Bronx Charter School for Excellence 6** shares its state ID with a school NCES lists as Girls Preparatory Charter School of the Bronx. I treated this as the same charter under a new name and used the NCES address.

## Limitations

- **Campus-level detail for Success Academy only.** Other networks also run multiple campuses, but they are shown at one address per charter. This means the map shows Success Academy's full footprint but understates other networks' footprints.
- **Success Academy popups have less detail.** Success Academy's website lists campuses by name, address, and grades, but not which charter each campus belongs to. So orange dots don't show district, authorizer, or enrollment.
- **Locations for non-Success Academy schools reflect 2024-25.** A school that has moved since then appears at its previous address.
- **Enrollment reflects 2024-25** and is not shown for the 10 newest schools.

## Next Steps

Most of these would need data beyond what this version uses.

| Idea | Data needed | Availability |
|---|---|---|
| Add district schools as a second layer | NCES or NYC Open Data school locations | Public, same API as this map |
| Automate the data refresh | Same sources as this map | Public, needs a scheduled job |
| Map every campus for all networks, not just Success Academy | NYC DOE school location data (LCGMS) | Public, needs cleaning and matching |
| Show current enrollment and capacity | NYC DOE enrollment data, SCA building capacity reports | Public, but released annually and capacity only covers DOE buildings |
| Show neighborhood demographics and transit access | Census ACS data, MTA transit data | Public, but takes real analysis to combine |
| Show where demand outpaces charter seats | Application and waitlist data | Not public, would need internal data |
| Natural language search | No new data, uses the existing dataset with an LLM | Possible now |

## Files

- `index.html`: the map, built with Leaflet with no build step
- `schools.js`: the joined school dataset (286 charter schools)
- `sa_campuses.js`: Success Academy campuses (59)
