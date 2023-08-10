## TITLE
In 500 years the protocol will break
## Summary
Overflow when type casting timestamp resulting in ```userOldPoint.bias``` being negative.
## Details
In about 486 years, the userOldPoint.bias value calculated by ```int128(int256(_oldLocked.end - block.timestamp));``` will overflow and turn negative.