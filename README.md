# Java
Java examples - ATM

package com.test;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
import java.util.Map;

public class Atm {
    Map<Integer, Integer> deposits = new HashMap<>();
    List<Integer> ignoreDenominations = new ArrayList<>();
    List<Integer> allowedDenominations;

    public Atm() {
        allowedDenominations = new ArrayList<>();
        allowedDenominations.add(1);
        allowedDenominations.add(5);
        allowedDenominations.add(10);
        allowedDenominations.add(20);
        //allowedDenominations.add(50);
        //allowedDenominations.add(100)
        for (int i : allowedDenominations) {
            deposits.put(i, 0);
        }
    }

    public void deposits(int... dep) {
        for (int i : dep) {
            deposit(i);
        }
    }

    private void deposit(int i) {
        if (i < 0) {
            System.out.println("Incorrect deposit amount : " + i);
            return;
        }
        if (i == 0) {
            System.out.println("Deposit amount cannot be zero");
            return;
        }
        if (deposits.containsKey(i)) {
            int val = deposits.get(i) + i;
            deposits.replace(i, val);
            System.out.println("Successfully deposited amount: " + i);
        } else {
            deposits.put(i, i);
            System.out.println("Successfully deposited amount: " + i);
        }
    }

    private Map<Integer, Integer> getEachDenominationCount() {
        Map<Integer, Integer> dCount = new HashMap<>();
        Iterator<Map.Entry<Integer, Integer>> iterator = deposits.entrySet().iterator();
        while (iterator.hasNext()) {
            Map.Entry<Integer, Integer> entry = iterator.next();
            dCount.put(entry.getKey(), entry.getValue() / entry.getKey());
        }
        return dCount;
    }


    public void withdraw(int withdrawAmount) {
        Map<Integer, Integer> withdraw = new HashMap<>();
        List<Integer> softIgnoreDenominations = new ArrayList<>();
        int tmpDespenseBalance = withdrawAmount;
        validateInput(withdrawAmount);
        while (tmpDespenseBalance != 0) {
            int maxDespenceDenomination = getAvailableMaxDenomination(ignoreDenominations, softIgnoreDenominations);
            if (maxDespenceDenomination == 0 && tmpDespenseBalance != 0) {
                // Not specific in the problem statement. This scenario can be handled based on business requirement
                System.out.println("Requested withdraw amount is less than total balance. But, Insufficient available denominations");
                return;
            }
            if (tmpDespenseBalance >= maxDespenceDenomination) {
                int tmp = tmpDespenseBalance % maxDespenceDenomination;
                int maxDenominationValueLimit = tmpDespenseBalance - tmp;
                maxDenominationValueLimit = (maxDenominationValueLimit < deposits.get(maxDespenceDenomination)) ? maxDenominationValueLimit : deposits.get(maxDespenceDenomination);
                deposits.replace(maxDespenceDenomination, (deposits.get(maxDespenceDenomination) - maxDenominationValueLimit));
                withdraw.put(maxDespenceDenomination, maxDenominationValueLimit / maxDespenceDenomination);
                if (deposits.get(maxDespenceDenomination) == 0) {
                    ignoreDenominations.add(maxDespenceDenomination);
                }
                tmpDespenseBalance = tmpDespenseBalance - maxDenominationValueLimit;
            } else {
                if (deposits.get(maxDespenceDenomination) == 0) {
                    ignoreDenominations.add(maxDespenceDenomination);
                } else {
                    softIgnoreDenominations.add(maxDespenceDenomination);
                }
            }
        }
        System.out.println("Dispensed : " + withdraw);
        System.out.println("Balance Denominations: " + getEachDenominationCount());
        System.out.println("Total Balance : " + getTotalDeposits());
    }

    private void validateInput(int withdrawAmount) {
        if (withdrawAmount < 0 || withdrawAmount == 0 || getAvailableMaxDenomination() == 0 || getTotalDeposits() < withdrawAmount) {
            System.out.println("Incorrect or Insufficient funds");
            return;
        }
    }

    int getAvailableMaxDenomination(List<Integer> ignoreDenominations, List<Integer> softIgnoreDenominations) {
        List<Integer> ignoredList = new ArrayList<>();
        for (int i : ignoreDenominations) {
            ignoredList.add(i);
        }
        for (int j : softIgnoreDenominations) {
            ignoredList.add(j);
        }
        return getMaxDenomination(ignoredList.toArray(new Integer[ignoredList.size()]));
    }


    private int getMaxDenomination(Integer... ignoreDenominations) {
        List<Integer> ints = Arrays.asList(ignoreDenominations);
        int maxVal = 0;
        Iterator<Map.Entry<Integer, Integer>> iterator = deposits.entrySet().iterator();
        while (iterator.hasNext()) {
            Map.Entry<Integer, Integer> entry = iterator.next();
            if (entry.getKey() > maxVal) {
                if (!ints.contains(entry.getKey())) {
                    maxVal = entry.getKey();
                }
            }
        }
        return maxVal;
    }

    private int getAvailableMaxDenomination() {
        return getAvailableMaxDenomination(ignoreDenominations, new ArrayList<>());
    }

    Integer getTotalDeposits() {
        int total = 0;
        Iterator<Map.Entry<Integer, Integer>> iterator = deposits.entrySet().iterator();
        while (iterator.hasNext()) {
            Map.Entry<Integer, Integer> entry = iterator.next();
            total = total + entry.getValue();
        }
        return total;
    }

    public static void main(String args[]) {
        Atm atm = new Atm();
        atm.deposits(10, 10, 10, 10, 10, 10, 10, 10, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5);
        System.out.println("Deposit Summary " + atm.getEachDenominationCount());
        System.out.println("Total Deposited Amount : " + atm.getTotalDeposits());

        atm.deposits(20, 20, 20, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 1, 1, 1, 1);
        System.out.println("Deposit Summary " + atm.getEachDenominationCount());
        System.out.println("Total Deposited Amount : " + atm.getTotalDeposits());

        System.out.println("****** ******* ******");
        System.out.println("Withdraw amount: " + 75);

        atm.withdraw(75);
        System.out.println("Balance Denominations : " + atm.getEachDenominationCount());

        System.out.println("Withdraw amount: " + 122);
        atm.withdraw(124);
        System.out.println("Balance Denominations : " + atm.getEachDenominationCount());

        System.out.println("Withdraw amount: " + 253);
        atm.withdraw(253);

        System.out.println("Withdraw amount: " + 0);
        atm.withdraw(0);

        System.out.println("Withdraw amount: " + -25);
        atm.withdraw(-25);
    }
}
