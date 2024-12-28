+++
title = 'macでparquetファイルを読む方法'
date = 2024-09-15T16:52:04+09:00
draft = false
categories = ['Engineering']
tags = ['parquet']
+++

## 概要
macでparquetファイルをコマンドラインで簡単に読む方法を紹介します。


## parquet-cliを使って読む

今回は Feastのサンプルで提供されている[Parquet](https://github.com/feast-dev/feast/tree/master/examples/podman_local/feature_repo/data)ファイルを読んでみます。

parquet-cliをbrewでmacにインストールする。

```
brew install parquet-cli
```


meta情報を確認する。

```
$ parquet meta driver_stats.parquet

File path:  driver_stats.parquet
Created by: parquet-cpp-arrow version 18.1.0
Properties:
        pandas: {"index_columns": [{"kind": "range", "name": null, "start": 0, "stop": 1807, "step": 1}], "column_indexes": [{"name": null, "field_name": null, "pandas_type": "unicode", "numpy_type": "object", "metadata": {"encoding": "UTF-8"}}], "columns": [{"name": "event_timestamp", "field_name": "event_timestamp", "pandas_type": "datetimetz", "numpy_type": "datetime64[ns]", "metadata": {"timezone": "UTC"}}, {"name": "driver_id", "field_name": "driver_id", "pandas_type": "int64", "numpy_type": "int64", "metadata": null}, {"name": "conv_rate", "field_name": "conv_rate", "pandas_type": "float32", "numpy_type": "float32", "metadata": null}, {"name": "acc_rate", "field_name": "acc_rate", "pandas_type": "float32", "numpy_type": "float32", "metadata": null}, {"name": "avg_daily_trips", "field_name": "avg_daily_trips", "pandas_type": "int32", "numpy_type": "int32", "metadata": null}, {"name": "created", "field_name": "created", "pandas_type": "datetime", "numpy_type": "datetime64[us]", "metadata": null}], "creator": {"library": "pyarrow", "version": "18.1.0"}, "pandas_version": "2.2.3"}
  ARROW:schema: /////xgGAAAQAAAAAAAKAA4ABgAFAAgACgAAAAABBAAQAAAAAAAKAAwAAAAEAAgACgAAAHAEAAAEAAAAAQAAAAwAAAAIAAwABAAIAAgAAABIBAAABAAAADsEAAB7ImluZGV4X2NvbHVtbnMiOiBbeyJraW5kIjogInJhbmdlIiwgIm5hbWUiOiBudWxsLCAic3RhcnQiOiAwLCAic3RvcCI6IDE4MDcsICJzdGVwIjogMX1dLCAiY29sdW1uX2luZGV4ZXMiOiBbeyJuYW1lIjogbnVsbCwgImZpZWxkX25hbWUiOiBudWxsLCAicGFuZGFzX3R5cGUiOiAidW5pY29kZSIsICJudW1weV90eXBlIjogIm9iamVjdCIsICJtZXRhZGF0YSI6IHsiZW5jb2RpbmciOiAiVVRGLTgifX1dLCAiY29sdW1ucyI6IFt7Im5hbWUiOiAiZXZlbnRfdGltZXN0YW1wIiwgImZpZWxkX25hbWUiOiAiZXZlbnRfdGltZXN0YW1wIiwgInBhbmRhc190eXBlIjogImRhdGV0aW1ldHoiLCAibnVtcHlfdHlwZSI6ICJkYXRldGltZTY0W25zXSIsICJtZXRhZGF0YSI6IHsidGltZXpvbmUiOiAiVVRDIn19LCB7Im5hbWUiOiAiZHJpdmVyX2lkIiwgImZpZWxkX25hbWUiOiAiZHJpdmVyX2lkIiwgInBhbmRhc190eXBlIjogImludDY0IiwgIm51bXB5X3R5cGUiOiAiaW50NjQiLCAibWV0YWRhdGEiOiBudWxsfSwgeyJuYW1lIjogImNvbnZfcmF0ZSIsICJmaWVsZF9uYW1lIjogImNvbnZfcmF0ZSIsICJwYW5kYXNfdHlwZSI6ICJmbG9hdDMyIiwgIm51bXB5X3R5cGUiOiAiZmxvYXQzMiIsICJtZXRhZGF0YSI6IG51bGx9LCB7Im5hbWUiOiAiYWNjX3JhdGUiLCAiZmllbGRfbmFtZSI6ICJhY2NfcmF0ZSIsICJwYW5kYXNfdHlwZSI6ICJmbG9hdDMyIiwgIm51bXB5X3R5cGUiOiAiZmxvYXQzMiIsICJtZXRhZGF0YSI6IG51bGx9LCB7Im5hbWUiOiAiYXZnX2RhaWx5X3RyaXBzIiwgImZpZWxkX25hbWUiOiAiYXZnX2RhaWx5X3RyaXBzIiwgInBhbmRhc190eXBlIjogImludDMyIiwgIm51bXB5X3R5cGUiOiAiaW50MzIiLCAibWV0YWRhdGEiOiBudWxsfSwgeyJuYW1lIjogImNyZWF0ZWQiLCAiZmllbGRfbmFtZSI6ICJjcmVhdGVkIiwgInBhbmRhc190eXBlIjogImRhdGV0aW1lIiwgIm51bXB5X3R5cGUiOiAiZGF0ZXRpbWU2NFt1c10iLCAibWV0YWRhdGEiOiBudWxsfV0sICJjcmVhdG9yIjogeyJsaWJyYXJ5IjogInB5YXJyb3ciLCAidmVyc2lvbiI6ICIxOC4xLjAifSwgInBhbmRhc192ZXJzaW9uIjogIjIuMi4zIn0ABgAAAHBhbmRhcwAABgAAACwBAADcAAAApAAAAHAAAAA0AAAABAAAAPz+//8AAAEKEAAAABgAAAAEAAAAAAAAAAcAAABjcmVhdGVkAGr///8AAAIAKP///wAAAQIQAAAAIAAAAAQAAAAAAAAADwAAAGF2Z19kYWlseV90cmlwcwBo////AAAAASAAAABg////AAABAxAAAAAcAAAABAAAAAAAAAAIAAAAYWNjX3JhdGUAAAAA0v///wAAAQCQ////AAABAxAAAAAgAAAABAAAAAAAAAAJAAAAY29udl9yYXRlAAYACAAGAAYAAAAAAAEAxP///wAAAQIQAAAAJAAAAAQAAAAAAAAACQAAAGRyaXZlcl9pZAAAAAgADAAIAAcACAAAAAAAAAFAAAAAEAAUAAgABgAHAAwAAAAQABAAAAAAAAEKEAAAACgAAAAEAAAAAAAAAA8AAABldmVudF90aW1lc3RhbXAACAAMAAYACAAIAAAAAAADAAQAAAADAAAAVVRDAAAAAAA=
Schema:
message schema {
  optional int64 event_timestamp (TIMESTAMP(NANOS,true));
  optional int64 driver_id;
  optional float conv_rate;
  optional float acc_rate;
  optional int32 avg_daily_trips;
  optional int64 created (TIMESTAMP(MICROS,false));
}


Row group 0:  count: 1807  16.88 B records  start: 4  total(compressed): 29.796 kB total(uncompressed):29.760 kB
--------------------------------------------------------------------------------
                 type      encodings count     avg size   nulls   min / max
event_timestamp  INT64     S _ R     1807      2.78 B     0       "2021-04-12T07:00:00.00000..." / "2024-12-28T14:00:00.00000..."
driver_id        INT64     S _ R     1807      0.07 B     0       "1001" / "1005"
conv_rate        FLOAT     S _ R     1807      5.42 B     0       "1.9221554E-4" / "0.9998668"
acc_rate         FLOAT     S _ R     1807      5.42 B     0       "2.1329636E-4" / "0.99993944"
avg_daily_trips  INT32     S _ R     1807      3.14 B     0       "0" / "999"
created          INT64     S _ R     1807      0.05 B     0       "2024-12-28T15:20:28.266000" / "2024-12-28T15:20:28.266000"
```

headで中を見る。
```
$ parquet head driver_stats.parquet

{"event_timestamp": 1734102000000000000, "driver_id": 1005, "conv_rate": 0.27734742, "acc_rate": 0.7152132, "avg_daily_trips": 823, "created": 1735399228266000}
{"event_timestamp": 1734105600000000000, "driver_id": 1005, "conv_rate": 0.57354224, "acc_rate": 0.9831811, "avg_daily_trips": 851, "created": 1735399228266000}
{"event_timestamp": 1734109200000000000, "driver_id": 1005, "conv_rate": 0.3287562, "acc_rate": 0.6172164, "avg_daily_trips": 116, "created": 1735399228266000}
{"event_timestamp": 1734112800000000000, "driver_id": 1005, "conv_rate": 0.045716193, "acc_rate": 0.032996926, "avg_daily_trips": 741, "created": 1735399228266000}
{"event_timestamp": 1734116400000000000, "driver_id": 1005, "conv_rate": 0.12863782, "acc_rate": 0.8951942, "avg_daily_trips": 534, "created": 1735399228266000}
{"event_timestamp": 1734120000000000000, "driver_id": 1005, "conv_rate": 0.9555806, "acc_rate": 0.62216556, "avg_daily_trips": 216, "created": 1735399228266000}
{"event_timestamp": 1734123600000000000, "driver_id": 1005, "conv_rate": 0.75297666, "acc_rate": 0.37602386, "avg_daily_trips": 954, "created": 1735399228266000}
{"event_timestamp": 1734127200000000000, "driver_id": 1005, "conv_rate": 0.46957988, "acc_rate": 0.6454945, "avg_daily_trips": 360, "created": 1735399228266000}
{"event_timestamp": 1734130800000000000, "driver_id": 1005, "conv_rate": 0.6702387, "acc_rate": 0.36532214, "avg_daily_trips": 396, "created": 1735399228266000}
{"event_timestamp": 1734134400000000000, "driver_id": 1005, "conv_rate": 0.019627139, "acc_rate": 0.528229, "avg_daily_trips": 833, "created": 1735399228266000}
```

スキーマの確認。

```
$ parquet schema driver_stats.parquet
{
  "type" : "record",
  "name" : "schema",
  "fields" : [ {
    "name" : "event_timestamp",
    "type" : [ "null", "long" ],
    "default" : null
  }, {
    "name" : "driver_id",
    "type" : [ "null", "long" ],
    "default" : null
  }, {
    "name" : "conv_rate",
    "type" : [ "null", "float" ],
    "default" : null
  }, {
    "name" : "acc_rate",
    "type" : [ "null", "float" ],
    "default" : null
  }, {
    "name" : "avg_daily_trips",
    "type" : [ "null", "int" ],
    "default" : null
  }, {
    "name" : "created",
    "type" : [ "null", {
      "type" : "long",
      "logicalType" : "local-timestamp-micros"
    } ],
    "default" : null
  } ]
}
```

## まとめ
macでparquetファイルを簡単に読む方法を紹介しました。
