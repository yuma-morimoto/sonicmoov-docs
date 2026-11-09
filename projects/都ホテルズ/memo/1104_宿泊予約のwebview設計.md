
>[!WARNING] SynXis（シナキシス）未実装
> 海外ホテルはTripla非経由のSynXis（シナキシス）への遷移が必要で、まだ実装してない。

組み立てるURL：
```
https://reservation.miyakohotels.ne.jp/booking/result?code=<施設コード>&key=<認証キー>&utm_source=miyako_plus_app&utm_medium=app
```

- 施設コード：Facility list → id（ホテル施設コード）
- 認証キー：[RC-API(APP)-1-07] → `payload.tripla.access_key`

- `HotelBookingWebViewPage`
	- `HotelBookingWebViewViewModel` 
		- -> `build()`, `retry()`
			- `HotelBookingRepository` 
				- -> `createBookingUri()`
					- request: [RC-API(APP)-1-07]




