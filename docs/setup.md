# APEX Setup

APEX Nitro requires some modifications in your APEX application.

### Build Option

APEX Nitro isn't meant to run in production. A build option is used to limit APEX Nitro to the development environment.

Head to `Shared Components > Build Options` and create a new build option with the following attributes:

Attribute | Value
--- | ---
Build Option | `DEVELOPMENT_ONLY`
Status | `Include`
Default on Export | `Exclude`

*Note: When you import an application in a different schema, anything tagged with the `DEVELOPMENT_ONLY` build option will not be run. If you re-import back into a development environment you'll need to manually change the status to `Include`.*

### Application Process
Head to `Shared Components > Application Processes` and create a new application process with the following attributes:

Attribute | Value | Comment
--- | --- | ---
Name | `APEX Nitro` |
Sequence | `-999` | Ensures this happens first
Process Point | `On Load: Before Header (page template header)` |
Condition | *see below #1* |
Source | *see below #2* |
Build Option | `DEVELOPMENT_ONLY` | Ensures this only gets run in a development environment

**#1 (condition)*
```sql
owa_util.get_cgi_env('APEX-Nitro') is not null
```

**#2 (source)*
```sql
-- Use one of the following depending on your files location
apex_application.g_flow_images := owa_util.get_cgi_env('APEX-Nitro');
-- apex_application.g_company_images := owa_util.get_cgi_env('APEX-Nitro');
-- apex_application.g_theme_file_prefix := owa_util.get_cgi_env('APEX-Nitro');
-- :G_APP_IMAGES := owa_util.get_cgi_env('APEX-Nitro');
```

Which one of the commented expression above is right for you?

Substitution String | Purpose | How to Use Examples
--- | --- | ---
apex_application.g_flow_images | Application Static Files | `#APP_IMAGES#js/app#MIN#.js` <br> `#APP_IMAGES#css/app#MIN#.css`
apex_application.g_company_images | Workspace Static Files | `#WORKSPACE_IMAGES#js/app#MIN#.js` <br> `#WORKSPACE_IMAGES#css/app#MIN#.css`
apex_application.g_theme_file_prefix | Theme Static Files | `#THEME_IMAGES#js/app#MIN#.js` <br> `#THEME_IMAGES#css/app#MIN#.css`
:G_APP_IMAGES | Custom Application Item that contains the path of your files. Name could be different. | `&G_APP_IMAGES.js/app#MIN#.js` <br> `&G_APP_IMAGES.css/app#MIN#.css`

---

### References
In APEX, you can reference your files at many levels

Level | Access Point
--- | ---
Application | Shared Components > User Interfaces > User Interface Details > JavaScript / Cascading Style Sheets > File URLs
Theme | Shared Components > Themes > Create / Edit Theme > JavaScript and Cascading Style Sheets > File URLs
Template | Shared Components > Templates > Edit Page Template > JavaScript / Cascading Style Sheet > File URLs
Page | Page Designer > Page X > JavaScript / CSS > File URLs
