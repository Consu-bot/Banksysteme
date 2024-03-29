# Banksysteme

package myproject;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Scanner;

public class Main {

    public static void main(String[] args) {
        System.out.println("Hey,it is your bank assistant,we are happy to see you!");
        System.out.println("Please type in what you wanna do.");
        choices(false);

        Scanner scanner = new Scanner(System.in);
        String choice = scanner.nextLine();

        CreateAccount createAccount = new CreateAccount();
        BankAccountService service = new BankAccountService();

        List<BankAccount> accounts = new ArrayList<>();
        Map<String, List<Integer>> accountMap = new HashMap<>();
        String accountName = null;

        while (choice!=null){
            switch (choice) {
                case  "login" : {
                    if (accountName == null) {
                        System.out.println("Please enter your name");
                        accountName = scanner.nextLine();
                        System.out.println("Hi, " + accountName + "!");
                    }
                    else {
                        System.out.println("you are logged in already");
                    }
                    break;
                }
                case "logout" : {
                    System.out.println("Bye, " + accountName + "!");
                    accountName = null;
                    break;
                }
                case "bye" : {
                    return;
                }
                default: {
                    if (accountName != null) {
                        String[] inputArgs = choice.split(" ");
                        int numberOfArgs = inputArgs.length;
                        
                        if (numberOfArgs == 1) {
                            int accountId = createAccount.executeCreateAccount(inputArgs[0], 0, accounts);
                            if (accountId != -1) {
                                accountMap.putIfAbsent(accountName, new ArrayList<>());
                                accountMap.get(accountName).add(accountId);

                                System.out.println("New account is created. The account number is " + accountId + " .");
                            }
                        } else if (numberOfArgs == 2) {
                            Integer bankAccountId = Integer.valueOf(inputArgs[1]);
                            List<Integer> existedBankAccountIds = accountMap.getOrDefault(accountName, new ArrayList<>());

                            double balance = service.executeGetBalance(inputArgs[0], numberOfArgs - 1, bankAccountId, existedBankAccountIds, accounts);
                            if (balance != -1) {
                                System.out.println("Your account balance is " + balance + " euro.") ;
                            }
                        } else if (numberOfArgs == 3) {
                            Integer bankAccountId = Integer.valueOf(inputArgs[1]);
                            Integer amount = Integer.valueOf(inputArgs[2]);
                            List<Integer> existedBankAccountIds = accountMap.getOrDefault(accountName, new ArrayList<>());

                            double balance = service.executeDeposit(inputArgs[0], numberOfArgs - 1, bankAccountId, amount, existedBankAccountIds, accounts);
                            if (balance != -1) {
                                System.out.println("Your account balance is " + balance + " euro.");
                            }
                        } else if (numberOfArgs == 4) {
                        		Integer bankAccountId = Integer.valueOf(inputArgs[2]);
                        		Integer amount = Integer.valueOf(inputArgs[3]);
                        		List<Integer> existedBankAccountIds = accountMap.getOrDefault(accountName, new ArrayList<>());

                        		double balance = service.executeWithdraw(inputArgs[0], numberOfArgs - 2, bankAccountId, amount, existedBankAccountIds, accounts);
                        		if (balance != -1) {
                        			System.out.println("Your account balance is " + balance + " euro.");
                        		}
                        }
                        else if (numberOfArgs == 5) {
                        	Integer senderAccountId = Integer.valueOf(inputArgs[2]);
                    		Integer receiverAccountId = Integer.valueOf(inputArgs[3]);
                    		Integer amount = Integer.valueOf(inputArgs[4]);
                    		List<Integer> existedBankAccountIds = accountMap.getOrDefault(accountName, new ArrayList<>());

                    		double balance = service.executeTransfer(inputArgs[0], numberOfArgs - 2, senderAccountId, receiverAccountId, amount, existedBankAccountIds, accounts);
                    		if (balance != -1) {
                    			System.out.println(amount + " euro is transferred from account number: " + senderAccountId  + " to account number: " + receiverAccountId);
                    		}
                        }
                        else {
                            System.out.println("incorrect input!");
                        }

                    } else {
                        System.out.println("Please log in");
                    }
                }
            }

            choices(accountName != null);
            choice = scanner.nextLine();
        }
    }

    private static void choices(boolean isLoggedIn) {
        System.out.println("\n");

        if (isLoggedIn) {
            System.out.println("0. Please type 'logout' to logout");
        } else {
            System.out.println("0. Please type 'login' to login");
        }
        System.out.println("1.If you want to create new account, type in 'create ' ");
        System.out.println("2.If you want to transfer money from account A to account B, type in 'transfer ', 'loginName ', <accountAId>, <accountBId, <amount>");
        System.out.println("3.If you want to get balance from your account, type in 'getBalance ', <accountId>");
        System.out.println("4.If you want to deposit money into your account, type in 'deposit ', <accountId> and <amount>");
        System.out.println("5.If you want to withdraw money from your account, type in 'withdraw  ','loginName ', <accountId> and <amount>");
        System.out.println("6.If you dont want to proceed, type in 'bye' ");
    }
}
