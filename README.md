# Configuring Nextcloud for Arabic Users
## Setting Language and Locales
1. From the user icon in the top navigation bar, Go to **Personal settings** -> **Language & locale**
2. Switch **Language** to **اللغة العربية**
3. Switch **Locale** to **Arabic(*your_country*)**
4. Set **First day of the week** to Sunday/Saturday
## Fixing Visual Bugs with Custom CSS
1. Go to app store in Nextcloud
2. Search for "Custom CSS"
3. Download and enable **Custom CSS**
4. From the user icon in the top navigation bar, Go to **Administration settings** -> **Theming** -> **Custom CSS**
5. Add CSS fixes
(Examples)
- Flip back arrow in when in RTL
```
:dir(rtl) .icon-arrow-back,
:dir(rtl) .icon-view-next,
:dir(rtl) .icon-view-previous,
:dir(rtl) [class*="chevron"] svg,
:dir(rtl) [class*="arrow-left"] svg,
:dir(rtl) [class*="arrow-right"] svg {
  transform: scaleX(-1);
```
- Reverse position of the file extension
```
.files-list__row-name-text {
  direction: ltr;
  unicode-bidi: isolate;
  display: inline-flex;
  justify-content: flex-end;
  max-width: 100%;
}
```
## Enabling Full text search using Elasticsearch
### Nextcloud Configuration
1. Go to app store in Nextcloud
2. Search for "Full text search"
3. Download and enable:
    - **Full text search**
    - **Full text search - Files**
    - **Full text search - Elasticsearch Platform**
4. From the user icon in the top navigation bar, Go to **Administration settings** -> **Full text search**
5. Set the **Search Platform** to **Elasticsearch**
6. Set the **Address of the Servlet** to *your Elasticsearch's URI*
7. Set the **Index** to "nextcloud"
8. Keep the **Analyzer Tokenizer** as "Standard"
### Elasticsearch Configuration
1. Add Arabic text search to Elasticsearch analyzer

Send this POST request to Elasticsearch:
```
curl -X DELETE {elasticsearch_URI}/nextcloud

curl -X PUT {elasticsearch_URI}/nextcloud -H 'Content-Type: application/json' -d '{
  "settings": {
    "number_of_replicas": 0,
    "analysis": {
      "analyzer": {
        "analyzer": {
          "tokenizer": "standard",
          "filter": ["lowercase", "decimal_digit", "arabic_normalization", "arabic_stem"]
        },
        "default": {
          "tokenizer": "standard",
          "filter": ["lowercase", "decimal_digit", "arabic_normalization", "arabic_stem"]
        }
      }
    }
  }
}'
```
Replace *'{elasticsearch_URI}'* with your Elasticsearch's URI

2. Index Nextcloud files using the Full text search
Run this command on the Nextcloud instance:
```
php occ fulltextsearch:index
```
If Nextcloud is hosted using the **Docker Compose file**, then run this command instead:
```
docker compose exec -it app php occ fulltextsearch:index
```
