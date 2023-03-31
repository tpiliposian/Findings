In one lock project, the developers implemented a time lock mechanism using a faulty approach.

To demonstrate the issue, let's write a similar contract and analyze the problem.
Suppose the minimum lock date is set to **1672612831**, which corresponds to **Jan 01 2023** in the Unix timestamp.
If we want to lock the contract for a second, we should call it with `_endDate` set to **1672612832**.

pragma solidity ^0.8.0;

contract datecheck {

    uint constant minLockDate = 1672612831;
    uint endDate;

    function lock(uint _endDate) public {
        require(_endDate >= minLockDate, "INVALID END DATE");
        endDate = _endDate + block.timestamp;
    }

    function viewendDate() public view returns(uint) {
        return endDate;
    }
}

However, if we sum up `_endDate` and `block.timestamp`, the result would be a date far in the future, specifically **3352772291**,
which corresponds to **Mar 30, 2076**. This means that the contract would be locked for a much longer period than intended,
potentially even beyond our lifetime.

The developers likely added this faulty implementation to prevent attackers from setting the lock date to a time in the past,
which would immediately release the locked tokens. However, a simple check to ensure that `_endDate` is greater than or equal
to the current timestamp would have sufficed and would have avoided this unintended consequence.
