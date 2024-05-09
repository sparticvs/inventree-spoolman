# Design Document

## Purpose
This document is intended to establish the concept of operations and design for implementation. It is to act as a guiding
beacon for developers.

## Background
[Spoolman](https://github.com/Donkie/Spoolman) is a 3D-printer filament inventory management tool. It is integrated into
other platforms like Fluidd, which is a WebUI front-end for Klipper/Moonraker powered 3D-printers.

Tracking filament is tricky. What is actually used by a print vs. what gcode says it needs are very different. Additionally,
InvenTree assumes that all allocated "parts" to a build order are consumed, which may not be the case when a print fails or 
is cancelled for some reason. Finally, InvenTree is not a filament inventory system, this plugin's goal is to reduce the
burden on the user to manage their filament inventory and allow the InvenTree software function accordingly.

## Concept of Operations
This plugin is targeting 2 separate use cases.

1. Where Spoolman is used as the SSoT (Single source of truth) for the filament inventory
2. Where InvenTree is used as the SSoT for the filament inventory

### Both Cases
This plugin will create a mapping of filament spools between Spoolman and InvenTree.

### Case 1: Spoolman as SSoT
This plugin will fetch information from Spoolman about the filament and provide the user with a view to import filament into
InvenTree's inventory.

Spools may be configured to be ignored by InvenTree, to allow for spools that may be for personal use but Spoolman is tracking them

Plugin will fetch the filament remaining on a spool and update the inventory totals in InvenTree (less the ammount allocated to build orders)

### Case 2: InvenTree as SSoT
This plugin will push information to Spoolman about the filament and provide the user with a view to create filament spools in Spoolman
that exist in InvenTree's inventory.

Plugin will update Spoolman's filament remaining for each spool based on the amount in InvenTree's part inventory + the amounts allocated
to build orders.

*Note: This approach is not really ideal and isn't prioritized. The rationale here is that remaining filament is calculated based on the
weight of the spool itself and the filament on the spool. InvenTree has no way to know what the weight of the plastic/cardboard spool is
which adds complexity to the math. Whereas, Spoolman does manage this type of information, which may mean that some hybrid truth may be
needed for this approach to work appropriately.*

### Quality Assurance Features
(low priority) This plugin will perform checking when "inventree-moonraker" is installed to see what filament is currently scanned in as
loaded into the printer. The check will ensure that the allocated parts (here the filament) matches what is expected to be
loaded and will prevent a job from being started where the filament does not match.

### Configuration View
A view is needed to provide the configuration information of the URL for the Spoolman webservice, as well as any authentication
credentials for authorizing access to the service.
A view needs to provide the ability for the admin to select the template part to build the filament inventory in.
